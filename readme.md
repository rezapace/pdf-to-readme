# PDF to README.md Conversion Tool
pdf converter
## Overview
This tool converts PDF files to a README.md file, removes unnecessary spaces and line breaks, splits the resulting markdown file into smaller chunks, and compresses them into a ZIP file.

## Dependencies
- Python 3
- PyMuPDF
- markdown2
- Google Colab

## Installation
Install the required packages:
```bash
!pip install pymupdf
!pip install markdown2
```

## Usage

### Step 1: Convert PDF to README.md

1. **Upload PDF files:**
    ```python
    from google.colab import files
    uploaded = files.upload()
    pdf_paths = list(uploaded.keys())
    ```

2. **Convert PDFs to README.md:**
    ```python
    import fitz  # PyMuPDF
    import markdown2

    def pdf_to_text(pdf_path):
        doc = fitz.open(pdf_path)
        text = ""
        for page_num in range(len(doc)):
            page = doc.load_page(page_num)
            text += page.get_text()
        return text

    def text_to_markdown_minified(text):
        minified_text = text.replace('\n', '').replace('  ', ' ')
        markdown_text = markdown2.markdown(minified_text)
        return markdown_text

    def save_to_file(content, file_path):
        with open(file_path, 'w') as file:
            file.write(content)

    def convert_pdfs_to_readme(pdf_paths, readme_path):
        combined_text = ""
        for pdf_path in pdf_paths:
            combined_text += pdf_to_text(pdf_path)
        readme_content = text_to_markdown_minified(combined_text)
        save_to_file(readme_content, readme_path)
        print(f"Conversion successful! README.md file saved at: {readme_path}")

    convert_pdfs_to_readme(pdf_paths, "README.md")
    ```

### Step 2: Split README.md into Smaller Files
```python
import os

def split_file(file_path, chunk_size=5120):
    with open(file_path, 'r') as file:
        content = file.read()

    base_dir = "split_files"
    if not os.path.exists(base_dir):
        os.makedirs(base_dir)

    for i in range(0, len(content), chunk_size):
        chunk = content[i:i + chunk_size]
        chunk_file_path = os.path.join(base_dir, f"README_part_{i//chunk_size + 1}.md")
        save_to_file(chunk, chunk_file_path)

    print(f"File split and saved in folder: {base_dir}")

split_file("README.md", chunk_size=5120)
```

### Step 3: Compress Split Files into ZIP
```python
import shutil

def zip_folder(folder_path, output_path):
    shutil.make_archive(output_path, 'zip', folder_path)
    print(f"Folder {folder_path} successfully zipped into {output_path}.zip")

folder_to_zip = "/content/split_files"
output_zip = "/content/split_files"
zip_folder(folder_to_zip, output_zip)
files.download(output_zip + ".zip")
```

## Stack Used
- **Python:** Programming language for scripting.
- **PyMuPDF:** Library for PDF processing.
- **markdown2:** Library for converting text to Markdown.
- **Google Colab:** Platform for running the code and handling file uploads/downloads.
Google Colab: Platform for running the code and handling file uploads/downloads.
