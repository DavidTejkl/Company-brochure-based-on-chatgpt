<!-- @format -->

## What does this project do?

The main goal of this project is to simplify the process of creating company brochures. Instead of manually browsing websites and copying information, this tool automatically visits a given URL, extracts relevant content, and then uses a large language model (LLM) to create a summary brochure.

## Key Components and How They Work

The project is written in Python and utilizes several libraries and concepts common in web scraping and natural language processing (NLP).

### 1. `project_company_brochure_chatGPT.ipynb` (Jupyter Notebook)

This is the main project file containing all the code. Jupyter Notebook is great for experimentation and iterative development, as it allows you to run code in sections and see results immediately.

#### Imports and Initialization

At the beginning of the notebook, you'll find imports of necessary libraries:

- `os`, `requests`, `json`, `typing`, `dotenv`, `bs4`, `IPython.display`, `openai`.
- `dotenv` is used to load the API key from a `.env` file, which is a good practice for securing sensitive information.
- The `OpenAI` library is crucial for interacting with language models.

#### `Website` Class

This class is the heart of the web scraping part of the project.

- **`__init__(self, url)`**: The class constructor that accepts a URL.
  - Uses `requests` to fetch the content of the webpage.
  - `BeautifulSoup` (`bs4`) is used to parse the HTML content.
  - Extracts the page title (`self.title`).
  - Removes irrelevant elements (scripts, styles, images, inputs) from the page body to obtain clean text content (`self.text`).
  - Collects all links (`<a>` tags) from the webpage (`self.links`).
- **`get_contents(self)`**: A method that returns a formatted string with the webpage title and text content.

#### System Prompts for LLM

The project defines two main system prompts for language models:

- **`link_system_prompt`**: This prompt instructs the LLM to select links from a given list that are relevant for a company brochure (e.g., "About Us", "Careers"). The response is expected in JSON format.
- **`system_prompt`**: This prompt instructs the LLM to create a short company brochure in Czech from the provided website content, targeting potential customers, investors, and job applicants.

#### Functions for LLM Interaction

- **`get_links_user_prompt(website)`**: Generates a user prompt for the LLM, containing a list of links from the given webpage and asking for the selection of relevant links.
- **`get_links(url)`**: This function takes a URL, creates a `Website` object, retrieves links, and then calls the LLM with `link_system_prompt` and `get_links_user_prompt` to identify relevant links. The result is parsed from JSON.
- **`get_all_details(url)`**: A key function that gathers all content for the brochure.
  - Fetches the content of the landing page.
  - Uses `get_links` to find relevant subpages.
  - For each relevant subpage (e.g., "About Us", "Careers"), it downloads its content and adds it to the overall text.
- **`get_brochure_user_prompt(company_name, url)`**: Generates the final user prompt for the LLM, containing the company name and all collected text from the webpages. The text is truncated to a maximum of 5000 characters to fit within LLM token limits.
- **`create_brochure(company_name, url)`**: This function takes the company name and URL, calls the LLM with `system_prompt` and `get_brochure_user_prompt`, and displays the resulting brochure in Markdown format.
- **`stream_brochure(company_name, url)`**: Similar to `create_brochure`, but utilizes streaming the LLM response, allowing the brochure to be displayed progressively as it is generated. This improves the user experience for longer generations.

## How to Run It?

1.  **Clone the repository**
2.  **Install dependencies:** Make sure you have all the necessary libraries installed.

3.  **Set up API key:** Create a `.env` file in the project's root directory and add your OpenAI API key to it:

    ```
    OPENAI_API_KEY="your_api_key_here"
    ```

    Remember that your API key should start with `sk-proj-`.

4.  **Run Jupyter Notebook:** Open `project_company_brochure_chatGPT.ipynb` in Jupyter Lab or VS Code and run the cells sequentially.
