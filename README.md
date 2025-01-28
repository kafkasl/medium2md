# Medium to Markdown Exporter

A simple Jupyter notebook to export your Medium posts to markdown format, including images.

## Features
- Extracts all posts from your Medium profile
- Downloads and saves images locally
- Converts posts to markdown with YAML frontmatter
- Preserves original URLs and titles

## Setup

1. Install requirements:
```bash
pip install -r requirements.txt
```

2. Open `Medium2Md.ipynb` in Jupyter and update the `url` variable with your Medium profile URL:
```python
url = "https://medium.com/@yourusername"
```

## Output Structure
```
posts/
  your-first-post.md
  another-post.md
  images/
    image1.jpg
    image2.jpg
```

Each markdown file includes YAML frontmatter with:
- Original title
- Medium URL
- List of related images