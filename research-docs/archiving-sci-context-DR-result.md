# **Strategies for Archiving Project Data from https://myscifair.com/fair**

## **I. Introduction**

The task of creating a comprehensive archive of projects hosted on the https://myscifair.com/fair website, particularly those listed within a dropdown menu and subsequently displayed, requires a systematic approach to web data extraction. This report outlines several technical options for achieving this archival objective, ranging from specialized crawling libraries and custom Python scripts to the integration of Large Language Model (LLM) APIs and sophisticated agent frameworks. The selection of the most appropriate method hinges on the specific characteristics of the target website, such as its accessibility and the technical implementation of its interactive elements, notably the project dropdown menu and the dynamic loading of project listings. This document provides a detailed examination of these options, their respective methodologies, and a comparative analysis to guide the selection process. The overarching goal is to ensure a complete and accurately structured collection of project information for archival purposes. The platform, MySciFair, is designed to assist students in showcasing their science projects, with features for uploading project details, research findings, and multimedia content, supported by teachers.1

## **II. Prerequisite: Website Accessibility and Content Loading Analysis**

Before embarking on any data extraction strategy, two fundamental prerequisites must be addressed: the current accessibility of the https://myscifair.com/fair webpage and a thorough analysis of how its content, particularly the project dropdowns and project listings, functions. These factors will critically influence the feasibility and choice of the subsequent technical approaches.

* **A. Confirm Website Accessibility**
  * The primary step is to verify that the target URL, https://myscifair.com/fair, is currently accessible. Previous attempts to analyze the website structure indicated that the website was inaccessible.2 If the site remains inaccessible, no automated scraping or archival process can proceed. All subsequent options discussed in this report presume that the website can be reached and its content loaded. The general purpose of MySciFair is to be a platform for scientific discovery and showcasing projects.3
* **B. Analyze Dropdown Menu and Project Listing Implementation**
  * Understanding the mechanism by which the project dropdown menus function and how project listings are displayed is paramount. The HTML snippets provided indicate the following:
    1. **Filter Dropdowns (Fair, Category, Division):**
       * The initial filter controls, including the "Fair" dropdown, are standard HTML \<select\> elements. For example, the main container for these filters appears to be div.bubble-element.Group.cnaLaIw1. The "Fair" dropdown seems tobe the first \<select class="bubble-element Dropdown dropdown-chevron"\> within this container.
       * The options within these dropdowns are standard \<option class="dropdown-choice"\> tags, with value attributes (e.g., "1348695171700984260\_\_LOOKUP\_\_1725050693238x111581500859744260") and visible text (e.g., "2025 Hawaii East District Science and Engineering Fair").
       * This static nature means the list of available fairs, categories, and divisions can be extracted directly from the initial HTML of the /fair page.
    2. **Project Listing Display (Dynamic Loading):**
       * After selecting a fair (and potentially other filters), projects are displayed in a repeating group, likely within a container like div.bubble-element.RepeatingGroup.cnaMgo1.
       * Crucially, as you've noted, **not all projects load initially; a scroll event is required to load more projects dynamically.** This is a critical factor for choosing an extraction tool, as it necessitates browser automation capable of simulating scrolling.
       * Each project is presented as a card (e.g., within div.bubble-element.group-item), containing elements for the project title (e.g., div.bubble-element.Text.cnaMgt1), project ID/code (e.g., div.bubble-element.Text.cnaMhaA1 div), category, and division.
       * Each project card also contains a "VIEW MORE" button (e.g., button.clickable-element.bubble-element.Button.cnaMgz1), which likely navigates to an individual project page (e.g., https://myscifair.com/project/{project\_id}).
    3. **Individual Project Page:**
       * The individual project page (e.g., https://myscifair.com/project/1723929472825x888760644717772800) contains more detailed information, including student names, school, district, and links to project files (PDFs).

The static nature of the filter dropdowns simplifies the initial step of selecting a fair. However, the dynamic scroll-to-load mechanism for project listings mandates the use of tools that can execute JavaScript and simulate user interactions like scrolling.

## **III. Option 1: Leveraging crawl4ai**

The user has identified crawl4ai as a potential tool. This library is an open-source web crawler and scraper specifically engineered for developers working with LLMs, AI agents, and modern data pipelines, offering features well-suited for extracting data from dynamic websites.4

* **A. Overview of crawl4ai Relevant Capabilities**
  * crawl4ai is designed for speed and efficiency, capable of executing JavaScript and waiting for asynchronous content to load, a critical feature for modern web applications like the project listing page on myscifair.com/fair.5
  * It supports deep crawling strategies (BFS, DFS, BestFirst) to explore websites beyond initial URLs.6
  * It offers multiple crawling strategies, including Playwright-based (default, for JS rendering) and HTTP-only (for simpler tasks).6 The Playwright strategy is essential here for handling the dynamic project loading.
  * Crucially for archival, it can generate clean, structured Markdown from web content, with filters to remove clutter like menus and ads, making the output suitable for LLMs and human readability.5
  * It features robust structured data extraction capabilities using both CSS/XPath selectors and LLM-driven interpretation.5 This allows for flexibility depending on the predictability of the project data's HTML structure.
  * The library also includes features like caching, stealth mode to avoid bot detection, and customizable hooks.5
* **B. Step-by-Step Implementation for Archiving Project List**
  1. **Installation and Setup:**
     * Install crawl4ai: pip install \-U crawl4ai
     * Run post-installation setup to install Playwright browser binaries: crawl4ai-setup
     * Verify installation: crawl4ai-doctor.4
  2. **Crawler Initialization and Fair Selection:**
     * Initially, one might use crawl4ai (or a simpler Requests \+ BeautifulSoup script, see Option 2A) to fetch https://myscifair.com/fair and parse the \<select\> element (likely the first one in div.bubble-element.Group.cnaLaIw1) to get a list of all available fairs and their value attributes.
     * For each fair value, construct the URL that displays projects for that fair (e.g., https://myscifair.com/fair?f={fair\_value\_here}). This might require observing network requests in browser developer tools when a fair is selected to confirm the URL structure or if it's purely client-side filtering.
  3. **Crawling and Extracting Projects for a Selected Fair:**
     Python
     import asyncio
     from crawl4ai import AsyncWebCrawler, CrawlerRunConfig, BrowserConfig, CacheMode
     from crawl4ai.extraction\_strategy import JsonCssExtractionStrategy
     \# from pydantic import BaseModel, HttpUrl \# For LLMExtractionStrategy
     \# from typing import List, Optional \# For LLMExtractionStrategy

     async def archive\_projects\_for\_fair(fair\_url):
         browser\_conf \= BrowserConfig(headless=True) \# Set to False to see browser

         \# JavaScript to scroll to the bottom of the page to load all projects
         \# This might need to be repeated or made more sophisticated
         \# (e.g., scroll, wait, check if new content loaded, repeat)
         scroll\_js \= """
         async () \=\> {
             let previousHeight;
             let newHeight \= document.body.scrollHeight;
             do {
                 previousHeight \= newHeight;
                 window.scrollTo(0, document.body.scrollHeight);
                 await new Promise(resolve \=\> setTimeout(resolve, 3000)); // Wait for content to load
                 newHeight \= document.body.scrollHeight;
             } while (newHeight \> previousHeight);
             return true;
         }
         """

         run\_conf \= CrawlerRunConfig(
             js\_code\_on\_page\_load=scroll\_js, \# Execute scrolling after page load
             wait\_for\_timeout=10000, \# Wait for up to 10s after JS execution for content
             extraction\_strategy=JsonCssExtractionStrategy(
                 schema\_definition={
                     "baseSelector": "div.bubble-element.group-item", \# Selector for each project card
                     "fields":
                 }
             ),
             cache\_mode=CacheMode.BYPASS \# Ensure fresh data for archival run
         )

         async with AsyncWebCrawler(config=browser\_conf) as crawler:
             result \= await crawler.arun(url=fair\_url, config=run\_conf)

             if result.success:
                 print(f"Successfully crawled {fair\_url}.")
                 if result.extracted\_content:
                     print("Extracted Projects (JSON):")
                     print(result.extracted\_content)
                     \# Further processing to save result.extracted\_content

                 \# print("\\nClean Markdown for Archival:")
                 \# print(result.markdown.fit\_markdown)
             else:
                 print(f"Crawling {fair\_url} failed: {result.error\_message}")
         return result.extracted\_content if result.success else None

     async def main\_archival():
         \# Example: Assume fair\_id 'HDSEF' was obtained from the dropdown
         \# The exact URL parameter needs to be confirmed by inspecting network traffic
         \# when a fair is selected on the website.
         \# The user provided: https://myscifair.com/fair?y=2025\&f=HDSEF
         example\_fair\_url \= "https://myscifair.com/fair?y=2025\&f=HDSEF" \# Replace with actual fair URL/params

         \# In a real scenario, loop through all fair URLs obtained in step 2
         projects \= await archive\_projects\_for\_fair(example\_fair\_url)
         if projects:
             \# Save projects to a file or database
             print(f"Archived {len(projects)} projects for {example\_fair\_url}")

     if \_\_name\_\_ \== "\_\_main\_\_":
         asyncio.run(main\_archival())

  4. **Handling Dynamic Loading (Scrolling):**
     * The js\_code\_on\_page\_load parameter in CrawlerRunConfig is used to execute JavaScript that scrolls the page, triggering the loading of more project items.7 The example scroll\_js attempts to scroll until no new content is loaded. This might need refinement based on the exact behavior of the site's infinite scroll.
     * wait\_for\_timeout or wait\_for\_selector (if a specific "loading complete" element appears) should be used to ensure content is loaded before extraction.
  5. **Extracting Project Card Details:**
     * **CSS-Based Extraction:** Use JsonCssExtractionStrategy with selectors matching the HTML structure of the project cards (e.g., baseSelector: "div.bubble-element.group-item"). The fields would target elements like div.bubble-element.Text.cnaMgt1 for the project name. Extracting the link to the individual project page might involve getting a project ID from an attribute or constructing it, then prepending https://myscifair.com/project/.
     * **LLM-Driven Extraction:** If the HTML structure of cards is inconsistent or extracting specific details like the project ID for the link is difficult, LLMExtractionStrategy can be used. Provide a Pydantic model for the project data and a prompt to guide the LLM.5
  6. **Archiving:**
     * result.extracted\_content will contain the JSON list of projects from the cards.
     * For a more complete archive, a second step could involve using crawl4ai or another method to visit each extracted project link (e.g., https://myscifair.com/project/{project\_id}) to get details like student names and PDF file URLs from the individual project page structure.
* **C. Pros and Cons**
  * **Pros:**
    * Specifically designed for complex, modern websites that rely heavily on JavaScript for content rendering (like the scroll-to-load project list).5
    * Integrated LLM capabilities allow for robust extraction even from less structured HTML.5
    * Effective at generating clean Markdown output.5
    * Actively maintained with features like deep crawling and various extraction strategies.6
  * **Cons:**
    * The learning curve for specific configurations (e.g., perfecting the scrolling JavaScript, complex extraction schemas) might be steeper than a simple script for fully static content.
    * Relies on Playwright, which is more resource-intensive than HTTP clients.

## **IV. Option 2: Custom Python Scripting Solutions**

Custom Python scripts offer fine-grained control. The choice of libraries depends on whether you're targeting the static filter dropdowns or the dynamic project listings.

* **A. For Initial Fair List Extraction (Static Dropdowns): Requests and BeautifulSoup**
  * **Applicability:** Suitable for extracting the list of fairs, categories, and divisions from the initial /fair page, as these dropdowns are standard HTML \<select\> elements embedded in the source.
  * **Methodology:**
    1. Fetch page content: page \= requests.get("https://myscifair.com/fair").9
    2. Parse HTML: soup \= BeautifulSoup(page.content, "html.parser").10
    3. Locate the "Fair" dropdown (e.g., the first select.bubble-element.Dropdown within div.bubble-element.Group.cnaLaIw1).
    4. Iterate through its \<option\> tags to get fair names (text) and values (value attribute).
  * **Pros:** Lightweight and fast for this initial static data extraction.
  * **Cons:** Cannot handle the JavaScript-driven scrolling required to load all projects on the subsequent project listing pages.
* **B. For Dynamic Project Listings (Requires Scrolling): Selenium or Playwright (Python libraries)**
  * **Applicability:** Essential for interacting with the project listing page for a selected fair, as it requires simulating scrolling to load all project cards.
  * **Methodology (Selenium example; Playwright is conceptually similar** 11**):**
    1. **Setup:** Install selenium and WebDriver (e.g., chromedriver).
    2. **Initialize Browser & Navigate:**
       Python
       from selenium import webdriver
       from selenium.webdriver.common.by import By
       from selenium.webdriver.support.ui import WebDriverWait, Select
       from selenium.webdriver.support import expected\_conditions as EC
       import time

       driver \= webdriver.Chrome()
       \# First, navigate to the main /fair page
       driver.get("https://myscifair.com/fair")

       \# Example: Select a fair from the dropdown
       \# This requires identifying the correct select element for Fairs
       \# Assuming it's the first select within the filter group:
       fair\_dropdown\_element \= WebDriverWait(driver, 10).until(
           EC.presence\_of\_element\_located((By.CSS\_SELECTOR, "div.bubble-element.Group.cnaLaIw1 select.bubble-element.Dropdown:nth-of-type(1)"))
       )
       select\_fair \= Select(fair\_dropdown\_element)
       \# select\_fair.select\_by\_visible\_text("Name of Fair") \# Or by value/index
       \# For example, to select "2025 Hawaii East District Science and Engineering Fair":
       \# select\_fair.select\_by\_visible\_text("2025 Hawaii East District Science and Engineering Fair")
       \# Or, using the value from the HTML:
       select\_fair.select\_by\_value('"1348695171700984260\_\_LOOKUP\_\_1725050693238x111581500859744260"')

       \# Wait for page to update or navigate to the fair's project listing URL directly if known
       time.sleep(5) \# Adjust wait as needed, or use WebDriverWait for a specific element indicating projects loaded

       \# Scroll to load all projects
       last\_height \= driver.execute\_script("return document.body.scrollHeight")
       while True:
           driver.execute\_script("window.scrollTo(0, document.body.scrollHeight);")
           time.sleep(3) \# Wait for new projects to load
           new\_height \= driver.execute\_script("return document.body.scrollHeight")
           if new\_height \== last\_height:
               break
           last\_height \= new\_height

       project\_cards\_data \=
       project\_elements \= driver.find\_elements(By.CSS\_SELECTOR, "div.bubble-element.group-item")
       for card\_element in project\_elements:
           try:
               title \= card\_element.find\_element(By.CSS\_SELECTOR, "div.bubble-element.Text.cnaMgt1").text
               code \= card\_element.find\_element(By.CSS\_SELECTOR, "div.bubble-element.Text.cnaMhaA1 div").text
               category \= card\_element.find\_element(By.CSS\_SELECTOR, "div.bubble-element.Text.cnaMgy1").text
               division \= card\_element.find\_element(By.CSS\_SELECTOR, "div.bubble-element.Text.cnaMgu1 div").text
               \# Extracting the project link might require getting an ID from the card
               \# or the 'VIEW MORE' button and constructing the URL.
               \# For example, if project ID is part of 'code' or a data attribute:
               \# project\_id \=... \# logic to extract ID
               \# project\_link \= f"https://myscifair.com/project/{project\_id}"
               project\_cards\_data.append({
                   "title": title, "code": code, "category": category, "division": division \#, "link": project\_link
               })
           except Exception as e:
               print(f"Error parsing a project card: {e}")

       print(project\_cards\_data)
       driver.quit()

    3. **Select Fair:** Use Selenium's Select class to choose a fair from the first dropdown.11 The page will then update to show projects for that fair (or navigate).
    4. **Handle Scrolling:** Implement a loop that repeatedly scrolls down the page (driver.execute\_script("window.scrollTo(0, document.body.scrollHeight);")), waits for new content to load (time.sleep() or WebDriverWait), and checks if the page height has changed. Continue until no new content is loaded.
    5. **Extract Project Card Data:** After all projects are loaded, use driver.find\_elements() with CSS selectors (e.g., By.CSS\_SELECTOR, "div.bubble-element.group-item") to get all project card elements. Iterate through these to extract title, ID/code, category, division, and the link to the individual project page (from the "VIEW MORE" button or by constructing it).
    6. **Archive Data:** Store the extracted list of project details. Optionally, for each project link, navigate to the individual project page to scrape further details (student names, PDF links) using similar Selenium techniques.
  * **Pros:** Full control over browser interactions, essential for the scrolling mechanism. Can handle any JavaScript-driven dynamic content.
  * **Cons:** Slower and more resource-intensive than Requests. Scripts can be brittle if class names or HTML structure changes significantly (though the "bubble-element" classes seem somewhat generic).

## **V. Option 3: Enhancing Extraction and Structuring with LLM APIs**

When the HTML structure within project cards or on individual project pages is inconsistent, or when extracting specific details (like student names or parsing free-form text for abstracts) becomes complex with selectors, Large Language Models (LLMs) offer a powerful alternative.15

* **A. Rationale for LLM Integration**
  * LLMs can interpret HTML semantically, making them more resilient to minor layout changes.15
  * They excel at transforming raw scraped HTML into clean, structured JSON, ideal for archival.16
  * Can reduce development time for complex selector logic.
* **B. Workflow**
  1. **Scrape Raw Content:** Use crawl4ai, Selenium, or Playwright (as per Option 1 or 2B) to get the HTML of the fully loaded project listing page (after all scrolling) or the HTML of individual project pages.
  2. **Pre-process (Optional but Recommended):**
     * To optimize LLM costs, feed only relevant HTML sections (e.g., the div.bubble-element.RepeatingGroup.cnaMgo1 for the project list, or div.bubble-element.Group.cnaLaOaN1 for a single project page).
     * Converting HTML to Markdown (e.g., with markdownify) can reduce token count and provide cleaner input for the LLM.17
  3. **Define Output Schema (e.g., Pydantic):**
     Python
     from pydantic import BaseModel, HttpUrl
     from typing import List, Optional

     class ProjectCard(BaseModel):
         projectName: str
         projectCode: Optional\[str\] \= None
         projectCategory: Optional\[str\] \= None
         projectDivision: Optional\[str\] \= None
         projectPageUrl: Optional\[HttpUrl\] \= None \# Link to the full project page

     class ProjectDetails(ProjectCard): \# Extends ProjectCard for individual page
         students: Optional\[List\[str\]\] \= None
         school: Optional\[str\] \= None
         district: Optional\[str\] \= None
         projectFileUrl: Optional\[HttpUrl\] \= None
         researchPlanUrl: Optional\[HttpUrl\] \= None
         \# abstract: Optional\[str\] \= None \# If an abstract is present

     class ArchivedProjects(BaseModel):
         fairName: str
         projects: List\[ProjectCard\] \# Or List if scraping individual pages

  4. **Prompt Engineering:** Construct a clear prompt instructing the LLM to extract details according to your Pydantic schema from the provided HTML/Markdown.
     * Example (conceptual for OpenAI, using structured output features 16):
       Python
       \# Assume 'project\_card\_html\_content' is the HTML of one 'div.bubble-element.group-item'
       \# Assume 'ProjectCard' is the Pydantic model

       prompt\_text \= f"""
       Extract the project information from the following HTML content of a project card.
       The projectPageUrl should be constructed as 'https://myscifair.com/project/' \+ the unique ID found in the projectCode (e.g., if projectCode is 'ANIM \- 101', the ID might be part of a data attribute or the numeric part, this needs clarification based on how IDs are formed for URLs).
       Return the extracted data as a JSON object that strictly conforms to the following JSON Schema:
       {ProjectCard.schema\_json(indent=2)}

       HTML Content:
       \---
       {project\_card\_html\_content}
       \---
       """

  5. **LLM API Call:** Use an LLM API (OpenAI 16, Anthropic 20, Google Gemini 22) that supports structured output or JSON mode.
  6. **Parse LLM Response:** Parse the JSON response into your Pydantic model.
* **C. Libraries and Tools:**
  * Official SDKs: openai, anthropic, google-generativeai.
  * Frameworks: LangChain (with with\_structured\_output 18), ScrapeGraphAI 24, llm-scraper.25
* **D. Pros and Cons**
  * **Pros:** Adaptable to inconsistent HTML, good for extracting semantic data, can reduce selector maintenance.
  * **Cons:** API costs, latency, potential for LLM inaccuracies ("hallucinations"), token limits require careful input management.

## **VI. Option 4: Employing an Agent Framework**

For orchestrating a multi-step archival process (e.g., iterating through all fairs, then all projects within each fair, including scrolling and navigation to individual project pages), AI agent frameworks like LangChain, CrewAI, or AutoGen offer a structured approach.26

* **A. Introduction to Agent Frameworks**
  * Agents are LLMs with instructions, tools (e.g., browser automation, API callers), and memory.
  * Useful for automating entire pipelines.
* **B. Conceptual Design for Project Archival Agent System**
  1. **Fair Enumeration Agent:**
     * **Goal:** Extract the list of all available fairs (names and values) from the initial dropdown on https://myscifair.com/fair.
     * **Tools:** HTTP request tool (like Requests \+ BeautifulSoup) or a simple browser interaction tool.
  2. **Project Listing Agent:**
     * **Goal:** For a given fair, navigate to its project listing page, perform all necessary scrolling to load all project cards, and extract summary data from each card (title, ID, category, division, link to full project page).
     * **Tools:** Browser automation tool (wrapping Selenium/Playwright or using crawl4ai's capabilities, or a tool like CrewAI's StagehandTool 29 or ScrapeWebsiteTool 30). The tool must be able to execute JavaScript for scrolling.
  3. **Project Detail Extraction Agent (Optional):**
     * **Goal:** For each project link obtained by the Project Listing Agent, navigate to the individual project page and extract detailed information (student names, school, PDF links, etc.).
     * **Tools:** Browser automation tool, CSS/XPath extraction tool, or LLM-based extraction tool.
  4. **Archival Agent:**
     * **Goal:** Collect all extracted data, structure it (e.g., into a nested JSON object per fair), and save it.
     * **Tools:** Data structuring tools, file I/O tools.
  5. **Orchestration:** A manager agent or a predefined workflow (e.g., using LangGraph 31, CrewAI's task sequence 32, AutoGen's group chat 34) would coordinate these agents.
* **C. Pros and Cons**
  * **Pros:** Modular, scalable for complex workflows, potentially more resilient with error handling.
  * **Cons:** Higher development overhead and complexity for a potentially simpler task if only one fair's projects are needed. Learning curve for the framework.

## **VII. Comparative Analysis and Recommendations**

Choosing the optimal method requires comparing options against criteria informed by the now clearer website structure.

* **A. Feature Comparison Table for Proposed Solutions** *(This table would be similar to the original, but emphasis would shift based on the dynamic scrolling requirement for project listings.)*

| Feature | crawl4ai | Python: Requests \+ BeautifulSoup (for fair list) | Python: Selenium / Playwright (for project list) | Python \+ LLM API (for extraction) | Agent Framework (e.g., LangChain) |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **Ease of Initial Setup** | Medium | Low | Medium | Medium to High | High |
| **Handling Static Dropdowns (Fair List)** | Good (HTTP or Playwright) | Excellent | Good (overhead) | N/A | Good (with HTTP tool) |
| **Handling Dynamic Content (Scroll-to-load)** | Excellent (Playwright, JS exec) 5 | Poor | Excellent 38 | N/A (relies on scraper) | Excellent (with browser tool) |
| **Simulating User Interaction (Scrolling)** | Good (via js\_code) 7 | N/A | Excellent | N/A | Excellent |
| **Built-in Data Structuring** | Yes (JSON, Markdown) 5 | No (custom code) | No (custom code) | Yes (to JSON via LLM) 16 | Yes (via LLM tools, custom code) |
| **Robustness to Minor HTML Changes** | Medium (CSS), High (LLM strategy) 5 | Low | Low to Medium | High (semantic extraction) | High (if using LLM tools) |
| **Execution Speed** | Medium | Fast (for static part) | Slow | Adds LLM latency | Slow |
| **Resource Intensity** | Medium | Low (for static part) | High | Adds LLM processing | High |
| **Suitability for Full Archival** | Very Good | Partial (fair list only) | Very Good | Excellent (structuring) | Excellent (orchestration) |

* **B. Recommendations Based on Scenarios**
  1. **Scope 1: Archiving Projects from a Single, Known Fair.**
     * **Recommendation:** **Option 1 (crawl4ai)** or **Option 2B (Custom Selenium/Playwright script)**.
       * crawl4ai offers a good balance with its built-in Playwright for scrolling and extraction strategies.5 The js\_code\_on\_page\_load for scrolling and JsonCssExtractionStrategy with the identified selectors would be key.
       * A custom Selenium/Playwright script provides maximum control over the scrolling logic and extraction from project cards.
     * **Enhancement:** If extracting details from the project cards (especially constructing the correct project URL or handling variations) or from individual project pages proves difficult with selectors, integrate **Option 3 (LLM API for extraction/structuring)**.
  2. **Scope 2: Archiving Projects from ALL Fairs listed in the Dropdown.**
     * **Initial Step:** Use **Option 2A (Requests \+ BeautifulSoup)** to get the list of all fairs and their values from the main /fair page's static dropdown.
     * **Main Process:** For each fair identified:
       * Employ **Option 1 (crawl4ai)** or **Option 2B (Selenium/Playwright)** as described in Scenario 1 to handle navigation (if fair selection changes URL) or filtering, scrolling, and project card extraction.
     * **Orchestration:** If the overall process becomes complex (managing state across many fairs, error handling, logging), consider **Option 4 (Agent Framework)** to manage the workflow of iterating through fairs and then scraping projects for each.
  3. **GOAL: Scope 3: Archiving Full Project Details (including PDFs from individual pages) for all projects from one or all fairs.**
     * This extends Scenarios 1 or 2\. After extracting project card data (including the link/ID for the individual project page), an additional step is needed:
       * Navigate to each individual project page (e.g., https://myscifair.com/project/{project\_id}).
       * Extract details like student names, school, district, and the URLs of the "Project File" and "Research Plan" PDFs. The PDF URLs are in the src attribute of the iframe.pdfobject elements.
       * Download the PDF files.
     * Tools like crawl4ai, Selenium/Playwright would be used for this navigation and extraction. LLMs (Option 3\) could assist in parsing details from the project page. An Agent Framework (Option 4\) would be very beneficial for managing this multi-level scraping task (Fair \-\> Project List \-\> Individual Project Details \-\> PDFs).
* **C. Suggested Phased Approach for the User**
  1. **Phase 1: Initial Fair List Extraction:** Use Requests \+ BeautifulSoup to parse https://myscifair.com/fair and extract all fair names and their corresponding values from the first \<select\> dropdown. This gives you the scope of fairs to archive.
  2. **Phase 2: Single Fair Project Listing:** Select one fair. Use Selenium/Playwright (Option 2B) or crawl4ai (Option 1\) to:
     * Navigate to the project listing for that fair (either by selecting from the dropdown and letting the page update, or by constructing the URL if selection changes it, e.g., https://myscifair.com/fair?y=2025\&f=HDSEF).
     * Implement robust scrolling logic to ensure all project cards are loaded.
     * Extract data from the project cards using CSS selectors (e.g., title, code, category, division, and try to derive the individual project page URL).
  3. **Phase 3: Individual Project Detail Extraction (Optional but Recommended for Full Archive):** For a few sample project URLs obtained in Phase 2, use Selenium/Playwright or crawl4ai to navigate to the individual project page. Extract detailed information, including student names (if present and structured) and the direct URLs to the PDF project files (from the iframe src attributes).
  4. **Phase 4: Scaling and Automation:**
     * If CSS selectors are brittle or extraction is complex, integrate LLMs (Option 3).
     * To archive all projects from all fairs, iterate the process from Phase 2 (and 3 if desired) for every fair obtained in Phase 1\.
     * If this scaled-up process is complex to manage, consider an Agent Framework (Option 4\) for orchestration.

## **VIII. Conclusion**

Successfully archiving the project list from https://myscifair.com/fair is achievable with a clear understanding of its structure. The initial filter dropdowns are static HTML, simplifying fair selection. However, the dynamic, scroll-to-load mechanism for project listings necessitates browser automation tools like Selenium, Playwright, or crawl4ai that can execute JavaScript and simulate user scrolling.

The choice of method depends on the desired scope:

* For a list of projects from a single fair, a focused script using Selenium/Playwright or crawl4ai is appropriate.
* For all projects across all fairs, an iterative approach combining static parsing for the fair list with dynamic scraping for each fair's projects is needed.
* For comprehensive archival including individual project details and PDF files, further navigation and extraction from project-specific pages will be required.

LLMs can enhance extraction robustness, and agent frameworks can help orchestrate complex, multi-stage archival tasks. A phased approach, starting with basic extraction and iteratively adding complexity for dynamic content and broader scope, is recommended.

#### **Works cited**

1. myscifair, accessed June 2, 2025, [https://myscifair.com/privacy](https://myscifair.com/privacy)
2. accessed December 31, 1969, [https://myscifair.com/fair](https://myscifair.com/fair)
3. MySciFair, accessed June 2, 2025, [https://myscifair.com/](https://myscifair.com/)
4. Crawl4AI Tutorial: A Beginner's Guide \- Apidog, accessed June 2, 2025, [https://apidog.com/blog/crawl4ai-tutorial/](https://apidog.com/blog/crawl4ai-tutorial/)
5. unclecode/crawl4ai: Crawl4AI: Open-source LLM Friendly ... \- GitHub, accessed June 2, 2025, [https://github.com/unclecode/crawl4ai](https://github.com/unclecode/crawl4ai)
6. Crawl4AI v0.5.0 Release Notes, accessed June 2, 2025, [https://docs.crawl4ai.com/blog/releases/0.5.0/](https://docs.crawl4ai.com/blog/releases/0.5.0/)
7. Crawl4AI: Best AI Web Crawling Open Source Tool (Firecrawl Open Source Alternatives), accessed June 2, 2025, [https://huggingface.co/blog/lynn-mikami/crawl-ai](https://huggingface.co/blog/lynn-mikami/crawl-ai)
8. Building RAG with Milvus and Crawl4AI, accessed June 2, 2025, [https://milvus.io/docs/build\_RAG\_with\_milvus\_and\_crawl4ai.md](https://milvus.io/docs/build_RAG_with_milvus_and_crawl4ai.md)
9. Using Python Requests Module with Dropdown Options | ProxiesAPI, accessed June 2, 2025, [https://proxiesapi.com/articles/using-python-requests-module-with-dropdown-options](https://proxiesapi.com/articles/using-python-requests-module-with-dropdown-options)
10. Beautiful Soup select \- Educative.io, accessed June 2, 2025, [https://www.educative.io/answers/beautiful-soup-select](https://www.educative.io/answers/beautiful-soup-select)
11. How to handle dropdown in Selenium Python? | BrowserStack, accessed June 2, 2025, [https://www.browserstack.com/guide/python-selenium-select-dropdown](https://www.browserstack.com/guide/python-selenium-select-dropdown)
12. How to handle Static and Dynamic Dropdown in Playwright \- Neova Solutions, accessed June 2, 2025, [https://www.neovasolutions.com/2023/01/12/how-to-handle-static-and-dynamic-dropdown-in-playwright/](https://www.neovasolutions.com/2023/01/12/how-to-handle-static-and-dynamic-dropdown-in-playwright/)
13. Actions | Playwright Python, accessed June 2, 2025, [https://playwright.dev/python/docs/input](https://playwright.dev/python/docs/input)
14. Learn Dropdown Handling through Select Class in Selenium \- LambdaTest, accessed June 2, 2025, [https://www.lambdatest.com/blog/handling-dropdown-in-selenium-webdriver-python/](https://www.lambdatest.com/blog/handling-dropdown-in-selenium-webdriver-python/)
15. How to do Web Scraping with LLMs for Your Next AI Project? \- ProjectPro, accessed June 2, 2025, [https://www.projectpro.io/article/web-scraping-with-llms/1081](https://www.projectpro.io/article/web-scraping-with-llms/1081)
16. Structured Outputs \- OpenAI API, accessed June 2, 2025, [https://platform.openai.com/docs/guides/structured-outputs](https://platform.openai.com/docs/guides/structured-outputs)
17. Web Scraping with Gemini AI in Python – Step-by-Step Guide, accessed June 2, 2025, [https://brightdata.com/blog/web-data/web-scraping-with-gemini](https://brightdata.com/blog/web-data/web-scraping-with-gemini)
18. Structured outputs \- ️ LangChain, accessed June 2, 2025, [https://python.langchain.com/docs/concepts/structured\_outputs/](https://python.langchain.com/docs/concepts/structured_outputs/)
19. sumitaryal/HTML-parser-using-LLM \- GitHub, accessed June 2, 2025, [https://github.com/sumitaryal/HTML-parser-using-LLM](https://github.com/sumitaryal/HTML-parser-using-LLM)
20. Overview \- Anthropic API, accessed June 2, 2025, [https://docs.anthropic.com/en/api/overview](https://docs.anthropic.com/en/api/overview)
21. Building with extended thinking \- Anthropic API, accessed June 2, 2025, [https://docs.anthropic.com/en/docs/build-with-claude/extended-thinking](https://docs.anthropic.com/en/docs/build-with-claude/extended-thinking)
22. Structured output | Gemini API | Google AI for Developers, accessed June 2, 2025, [https://ai.google.dev/gemini-api/docs/structured-output](https://ai.google.dev/gemini-api/docs/structured-output)
23. How to return structured data from a model | 🦜️ LangChain, accessed June 2, 2025, [https://python.langchain.com/docs/how\_to/structured\_output/](https://python.langchain.com/docs/how_to/structured_output/)
24. ScrapeGraphAI Tutorial \- Getting Started with LLMs Web Scraping \- ScrapingAnt, accessed June 2, 2025, [https://scrapingant.com/blog/scrapegraphai-llms-web-scraping](https://scrapingant.com/blog/scrapegraphai-llms-web-scraping)
25. mishushakov/llm-scraper: Turn any webpage into ... \- GitHub, accessed June 2, 2025, [https://github.com/mishushakov/llm-scraper](https://github.com/mishushakov/llm-scraper)
26. Top 12 Frameworks for Building AI Agents in 2025 \- Bright Data, accessed June 2, 2025, [https://brightdata.com/blog/ai/best-ai-agent-frameworks](https://brightdata.com/blog/ai/best-ai-agent-frameworks)
27. Top 7 Free AI Agent Frameworks \- Botpress, accessed June 2, 2025, [https://botpress.com/blog/ai-agent-frameworks](https://botpress.com/blog/ai-agent-frameworks)
28. Generative AI Agents | Oracle, accessed June 2, 2025, [https://www.oracle.com/artificial-intelligence/generative-ai/agents/](https://www.oracle.com/artificial-intelligence/generative-ai/agents/)
29. Stagehand Tool \- CrewAI, accessed June 2, 2025, [https://docs.crewai.com/tools/stagehandtool](https://docs.crewai.com/tools/stagehandtool)
30. Leveraging CrewAI's Built-in Tools for Web Scraping | CodeSignal ..., accessed June 2, 2025, [https://codesignal.com/learn/courses/expanding-crewai-capabilities-and-integration/lessons/leveraging-crewais-built-in-tools-for-web-scraping](https://codesignal.com/learn/courses/expanding-crewai-capabilities-and-integration/lessons/leveraging-crewais-built-in-tools-for-web-scraping)
31. Web Scraping With LangChain & Oxylabs API, accessed June 2, 2025, [https://oxylabs.io/blog/langchain-web-scraping](https://oxylabs.io/blog/langchain-web-scraping)
32. How to Scrape Web Data With CrewAI & Oxylabs Scraper API, accessed June 2, 2025, [https://oxylabs.io/resources/integrations/crewai](https://oxylabs.io/resources/integrations/crewai)
33. Building Multi-Agent Systems With CrewAI \- A Comprehensive Tutorial \- Firecrawl, accessed June 2, 2025, [https://www.firecrawl.dev/blog/crewai-multi-agent-systems-tutorial](https://www.firecrawl.dev/blog/crewai-multi-agent-systems-tutorial)
34. Web Scraping using Apify Tools | AutoGen 0.2 \- Microsoft Open Source, accessed June 2, 2025, [https://microsoft.github.io/autogen/0.2/docs/notebooks/agentchat\_webscraping\_with\_apify/](https://microsoft.github.io/autogen/0.2/docs/notebooks/agentchat_webscraping_with_apify/)
35. AutoGen Tutorial: Build Multi-Agent AI Applications \- DataCamp, accessed June 2, 2025, [https://www.datacamp.com/tutorial/autogen-tutorial](https://www.datacamp.com/tutorial/autogen-tutorial)
36. Web Scraping With AutoGen Tutorial \- Oxylabs, accessed June 2, 2025, [https://oxylabs.io/resources/integrations/autogen](https://oxylabs.io/resources/integrations/autogen)
37. Extracting Results with an Agent — AutoGen \- Microsoft Open Source, accessed June 2, 2025, [https://microsoft.github.io/autogen/stable//user-guide/core-user-guide/cookbook/extracting-results-with-an-agent.html](https://microsoft.github.io/autogen/stable//user-guide/core-user-guide/cookbook/extracting-results-with-an-agent.html)
38. Top 7 Python Web Scraping Libraries \- Bright Data, accessed June 2, 2025, [https://brightdata.com/blog/web-data/python-web-scraping-libraries](https://brightdata.com/blog/web-data/python-web-scraping-libraries)