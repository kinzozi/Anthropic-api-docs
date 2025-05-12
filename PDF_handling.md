Okay, here is the content of the PDF converted to markdown:

# PDF support - Anthropic

Process PDFs with Claude. [cite: 1] Extract text, analyze charts, and understand visual content from your documents. [cite: 2] You can now ask Claude about any text, pictures, charts, and tables in PDFs you provide. [cite: 3]

Some sample use cases: [cite: 4]
* Analyzing financial reports and understanding charts/tables [cite: 4]
* Extracting key information from legal documents [cite: 4]
* Translation assistance for documents [cite: 4]
* Converting document information into structured formats [cite: 4]

## Before you begin

### Check PDF requirements
Claude works with any standard PDF. [cite: 5] However, you should ensure your request size meet these requirements when using PDF support: [cite: 5]

| Requirement             | Limit                                   |
| :---------------------- | :-------------------------------------- |
| Maximum request size    | 32MB                                    |
| Maximum pages per request | 100                                     |
| Format                  | Standard PDF (no passwords/encryption) |

Please note that both limits are on the entire request payload, including any. [cite: 5]

## Supported platforms and models
PDF support is currently available on Claude 3.7 Sonnet (claude-3-7-sonnet-20250219), both Claude 3.5 Sonnet models (claude-3-5-sonnet-20241022 claude-3-5-sonnet-20240620), and Claude 3.5 Haiku (claude-3-5-haiku-20241022) via direct API access and Google Vertex AI. [cite: 6] This functionality will be supported on Amazon Bedrock soon. [cite: 7]

## Process PDFs with Claude

### Send your first PDF request
Let's start with a simple example using the Messages API. You can provide PDFs to Claude in two ways: [cite: 8]

1.  As a base64-encoded PDF in document content blocks [cite: 8]
2.  As a URL reference to a PDF hosted online [cite: 8]

### Option 1: URL-based PDF document
The simplest approach is to reference a PDF directly from a URL. [cite: 8]

(The document includes code snippets for Shell, Python, TypeScript, and Java for providing PDFs via URL and base64 encoding, which are not fully transcribed here for brevity but are present in the original PDF pages 8-10, 13-14).

## How PDF support works
When you send a PDF to Claude, the following steps occur: [cite: 19]
1.  The system extracts the contents of the document. [cite: 20] The system converts each page of the document into an image. [cite: 20] The text from each page is extracted and provided alongside each page’s image. [cite: 21]
2.  Claude analyzes both the text and images to better understand the document. [cite: 22] Documents are provided as a combination of text and images for analysis. [cite: 23] This allows users to ask for insights on visual elements of a PDF, such as charts, diagrams, and other non-textual content. [cite: 24]
3.  Claude responds, referencing the PDF's contents if relevant. [cite: 25] Claude can reference both textual and visual content when it responds. [cite: 25] You can further improve performance by integrating PDF support with: [cite: 26]
    * Prompt caching: To improve performance for repeated analysis. [cite: 26]
    * Batch processing: For high-volume document processing. [cite: 27]
    * Tool use: To extract specific information from documents for use as tool inputs. [cite: 27]

## Estimate your costs
The token count of a PDF file depends on the total text extracted from the document as well as the number of pages. [cite: 19] You can use token counting to estimate costs for your specific PDFs. [cite: 29] Standard API pricing applies with no additional PDF fees. [cite: 31]

* **Text token costs:** Each page typically uses 1,500-3,000 tokens per page depending on content density. [cite: 31]
* **Image token costs:** Since each page is converted into an image, the same image-based cost calculations are applied. [cite: 31]

## Optimize PDF processing

### Improve performance
Follow these best practices for optimal results: [cite: 30]
* Place PDFs before text in your requests [cite: 32]
* Use standard fonts [cite: 32]
* Ensure text is clear and legible [cite: 32]
* Rotate pages to proper upright orientation [cite: 32]
* Use logical page numbers (from PDF viewer) in prompts [cite: 32]
* Split large PDFs into chunks when needed [cite: 32]
* Enable prompt caching for repeated analysis [cite: 32]

### Scale your implementation
For high-volume processing, consider these approaches: [cite: 30]

**Use prompt caching** [cite: 30]
Cache PDFs to improve performance on repeated queries. [cite: 30]

**Process document batches** [cite: 33]
Use the Message Batches API for high-volume workflows. [cite: 33]

## Next steps
* **Try PDF examples:** Explore practical examples of PDF processing in our cookbook recipe. [cite: 35]
* **View API reference:** See complete API documentation for PDF support. [cite: 36]
