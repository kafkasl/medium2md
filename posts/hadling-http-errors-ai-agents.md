# Handling HTTP Errors in AI Agents: Lessons from the Field


If you've been working with AI agents that make API calls, you've probably experienced this frustrating scenario: Your agent keeps failing when making HTTP requests, and you can't figure out why. The agent attempts to call an API, gets an error, and either keeps trying the same approach or gives up entirely.

![HTTP 422 Error with missing fields](images/http422error.jpg)


Your first instinct might be to improve the agent's instructions or add more detailed documentation about the API. While that helps, there's a critical piece that's often overlooked: **making sure your agent can actually see the error details**.

Here's the thing - AI agents aren't like traditional software that follows explicit error handling logic you've coded. They're more like junior developers who need to see the full error message to understand what went wrong. When an API returns a 422 Unprocessable Entity error with details like `missing required field: 'first_name'`, a human developer would immediately know what to fix. But your AI agent? It might just see `HTTP Error 422` if you haven't set up your error handling correctly.

We discovered this the hard way when we enhanced the [Replit Agent to buy domains](https://x.com/positiveblue2/status/1886564136156635396) and manage their DNS. In this situation we did not have access to the prompts, but we were missing a simpler solution: ensuring the complete error message, including the response body, was visible to the agent.

As Hamel Husain noted in his article ["What we learned from a year of building with LLMs"](https://www.oreilly.com/radar/what-we-learned-from-a-year-of-building-with-llms-part-ii/), monitoring your LLM inputs and outputs daily is crucial. With AI agents, this monitoring needs to extend to those middle steps - especially the error responses from API calls that the agent might be handling internally.

Let's explore how to fix this properly, so your AI agents can self-correct when they encounter API errors instead of spinning their wheels or requiring your intervention.


## One SDK, Multiple Agents

At first glance, the [Sherlock Domains Python SDK](https://github.com/fewsats/sherlock-python) seems straightforward. It's a clean interface for managing domain names and DNS records, with methods for searching, purchasing, and configuring domains. We designed it to be AI-friendly, with clear method names and parameters. 

When we first exposed this SDK to [Claudette](https://claudette.answer.ai/core.html) (an extremely convenient Claude wrapper from answer.ai) through tool calling, everything worked surprisingly well. The model could search for domains, understand the response format, and even handle the purchase flow with minimal documentation. It was a promising start that made us believe we had created a truly AI-friendly SDK.

Then we tried the same integration with a smaller model - Llama 3 running locally. The results were non-surprisingly different. The model could do basic searches but struggled to chain many API calls. For example, in order to buy a domain, you need to provide the ID returned by the search. Llama would get this wrong. Our first instinct was to add more documentation to the tools.

This helped pretty much, and it started working most of the time, but I wouldn't call it reliable.

The most interesting case emerged when we integrated with the [Replit Agent](https://docs.replit.com/replitai/agent) during a hackathon. We were fortunate to collaborate with [Kody](https://x.com/kodylow), a member of the Replit team, but as anyone who's participated in hackathons knows, the mix of creativity, caffeine, and code flying in all directions can be intense.

In this high-pressure environment, we found ourselves caught in a classic debugging loop - repeatedly yolo tweaking the [`llms.txt`](https://raw.githubusercontent.com/Fewsats/sherlock-python/refs/heads/main/llms.txt) file, only to find the same issues persisting. We weren't even aiming for perfect one-shot execution; we just wanted the system to be robust enough to self-correct when needed or to ask the user for missing information.

One persistent issue was that the Replit Agent would consistently try to set contact information using a single `name` field instead of the required `first_name` and `last_name` fields. This was puzzling since our documentation clearly showed the correct format:

```python
# Incorrect approach the agent kept trying
s.set_contact_information(
    name="John Doe",  # Wrong! This parameter doesn't exist
    email="john@example.com",
    # ...other fields
)

# Correct approach shown in our documentation
s.set_contact_information(
    first_name="John",
    last_name="Doe",
    email="john@example.com",
    # ...other fields
)
```

When this failed, our SDK would use [`response.raise_for_status()`](https://3.python-requests.org/user/quickstart/#:~:text=raise_for_status()) which raises an exception on error codes. However, it does not provide any details in the response body. It would show only: `HTTP Error 422: Unprocessable Entity`

But the actual response was:
```
{
   "detail":[
      {
         "type":"missing",
         "loc":[
            "body",
            "data",
            "first_name"
         ],
         "msg":"Field required"
      },
      {
         "type":"missing",
         "loc":[
            "body",
            "data",
            "last_name"
         ],
         "msg":"Field required"
      }
   ]
}
```

The agent would then enter a doom loop of trying random variations without ever addressing the actual problem. It was quite pitiful to see. 

Once we modified our SDK to surface the complete error details, the agent immediately adapted - correctly splitting the name into first and last name components and proceeding with the operation. The agent was capable all along; it just needed to see the full picture.


## The Many Faces of API Errors

When working with HTTP APIs, error responses come in various formats. This diversity creates a challenge for AI agents trying to understand what went wrong. Let's explore the common patterns:

### REST APIs: Status Codes and Response Bodies

REST APIs signal errors in several ways, with critical details often buried in the response body:

1. **Standard REST Error Responses**: Return a non-200 status code with a JSON body containing error details:
   ```json
   // 422 Unprocessable Entity
   {
     "detail": [
       {
         "type": "missing",
         "loc": ["body", "data", "first_name"],
         "msg": "Field required"
       },
       {
         "type": "missing",
         "loc": ["body", "data", "last_name"],
         "msg": "Field required"
       }
     ]
   }
   ```

2. **Minimalist Error Responses**: Return only a status code with minimal text:
   ```
   // 400 Bad Request
   "Invalid parameters: first_name and last_name required, got 'name' instead"
   ```

3. **Custom Error Formats**: Each API might structure errors differently:
   ```json
   // 400 Bad Request
   {
     "errors": {
       "name": ["must be split into first_name and last_name fields"]
     },
     "code": "validation_error"
   }
   ```

4. **Success Status with Error Content**: Some APIs return 200 OK even for errors, indicating the error in the response body:
   ```json
   // 200 OK (but contains an error!)
   {
     "success": false,
     "error": "Contact information requires first_name and last_name, not name",
     "error_code": "INVALID_PARAMETERS"
   }
   ```

### GraphQL: 200 OK with Errors in Response

GraphQL APIs typically return 200 OK status codes even when errors occur, placing error information in the response body:

```json
// 200 OK (but contains errors!)
{
  "data": null,
  "errors": [
    {
      "message": "Cannot create contact with 'name'. Use 'first_name' and 'last_name' instead",
      "path": ["setContactInformation", "name"],
      "extensions": { "code": "BAD_USER_INPUT" }
    }
  ]
}
```

### The Common Mistake in AI Agent Integration 

The crux of the problem becomes clear: **Most HTTP libraries raise exceptions based on status codes alone, completely discarding the valuable error details in the response body.**

In Python, this typically happens with code like:

```python
response = requests.post(url, json=payload)
response.raise_for_status()  # Raises exception on 4xx/5xx, but loses response body
data = response.json()  # Never reaches this line if there's an error
```

For human developers, this is manageable - we catch the exception, print it, and if needed, manually inspect the response to see what went wrong. But for AI agents, this creates a blind spot. The agent only sees `HTTPError: 422 Unprocessable Entity` without the crucial information that it needs to use `first_name` and `last_name` instead of `name`.

Even more insidious are the cases where APIs return 200 OK status codes with errors in the body. The error check might pass, but the response still contains errors that need to be handled differently.

### Beyond Simple Error Handling

The approach we've shown above - using `raise_for_status()` without capturing response details - is widespread but inadequate for AI agent integration. While there's no silver bullet that works for all APIs, the key principle is ensuring error details remain visible.

For example, a more agent-friendly approach might look like:

```python
response = requests.post(url, json=payload)
try: 
    response.raise_for_status()
except HTTPError as e:
    # Raise a custom exception that includes both the original error and the payload
    raise CustomHTTPError(str(e), payload=r.text)
    
return response.json()
```

This preserves both traditional exception handling (for human developers) and detailed error information (for AI agents). But even this approach requires adaptation for different API styles and error patterns.

The important insight isn't a specific implementation but the principle: **error visibility enables AI agent self-correction**. How you implement this will depend on your specific APIs, tools, and development patterns.

## Designing SDKs for Both Humans and AI

For the keen observer, there's an interesting paradox in our approach: If LLMs process everything as text anyway, why not just return the status code and the entire response text for all API calls? Why bother with structured exceptions and payloads?

This highlights a fundamental challenge in designing tools for the emerging AI-augmented development ecosystem: **we're building for two very different types of users simultaneously**.

### The Two-Audience Problem

Traditional SDKs are designed exclusively for human developers who expect:
- Structured data (dictionaries, objects) for easy manipulation
- Exception-based error handling that follows language conventions
- Abstractions that hide HTTP implementation details
- Type hints and IDE autocompletion support

AI agents, on the other hand, might be perfectly content with:
- Plain text that describes what happened
- Simple success/failure flags with detailed error messages
- Verbose information that would be tedious for humans to parse

In our Sherlock SDK example, we could have simply returned:
```
"HTTP Error 422: Unprocessable Entity. The server responded with: {'detail':[{'type':'missing','loc':['body','data','first_name'],'msg':'Field required'},{'type':'missing','loc':['body','data','last_name'],'msg':'Field required'}]}"
```

And the LLM would likely understand exactly what to do!

![HTTP 422 Error with missing fields](images/ai-agent-422.jpg)


### Why We're Not There Yet

Despite this simplicity, there are several reasons why returning plain text isn't the optimal solution today:

1. **Development and Testing**: Human developers still need to build and test these systems before AI agents use them.

2. **Hybrid Workflows**: Many projects involve collaboration between humans and AI, requiring interfaces that work well for both.

3. **Debugging**: When things go wrong, humans need to step in and understand what happened.

4. **SDK Evolution**: As SDKs grow, maintaining a consistent interface helps both humans and AI agents adapt to changes.


## The Future of APIs: `plain/text` is all you need

The immediate lesson when dealing with HTTP SDKs is clear: **Make error details visible to your AI agents.**

But there's a broader implication here. The Sherlock SDK example reveals a fundamental tension in current software development: we're building tools that need to serve both traditional human developers and AI agents simultaneously, despite their different needs.

For humans, we need structured data, familiar exception patterns, and concise information. For AI agents, verbose text descriptions with complete details often work better. Today's solution - enhanced exceptions with detailed payloads - represents a pragmatic middle ground for this transitional period.

Looking ahead, we can envision a future where SDKs designed exclusively for AI consumption abandon structured formats entirely in favor of plain text. When an AI agent is the consumer, the traditional benefits of structured data - type safety, schema validation, efficient parsing - become less relevant. What matters more is information density and context. A verbose error message explaining exactly what went wrong and how to fix it is far more valuable than a perfectly formatted but information-poor exception.

This shift becomes even more pronounced when we consider the next step: servers powered by AI agents. In this scenario, both client and server understand natural language, eliminating the need for predefined structures. The agents can negotiate formats dynamically - just as we see LLMs communicate with humans today, seamlessly switching between natural language, JSON, code snippets, or any other format based on the task requirements.

In such a world, API design becomes a fluid conversation rather than a rigid contract. The client could request "Please format this as a table for human viewing" or "I need the raw data for processing," and the server would respond appropriately. Format negotiation happens in natural language, not through content-type headers and schema definitions.

Eventually, as AI-to-AI communication evolves, they might even develop more efficient ways to exchange information that transcend human language entirely - specialized representations optimized for machine understanding.

For now, though, the key insight remains: **your AI agent can only be as good as the information it sees**. By ensuring complete error visibility, you unlock the self-healing capabilities of these systems - allowing them to adapt, learn, and overcome challenges without constant human intervention.

Whether you're building AI-enabled tools or integrating with existing services, remember that sometimes the most powerful enhancement isn't a more sophisticated model or a better prompt - it's simply ensuring that your agent can see the full picture, especially when things go wrong.
