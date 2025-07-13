**For AI Agent Use: Key Instructions for Populating the \<DeepResearchPrompt\>**

* Your primary goal is to help a user fill the \<DeepResearchPrompt\> XML template defined below.
* The core XML structure is:
  * \<DeepResearchPrompt\> (root)
  * \<ResearchObjective\>
  * \<ContextualBackground\>
  * \<InputParameters\>
  * \<AnalyticalFramework\>
  * \<OutputSpecifications\>
  * \<IterativeRefinementNotes\>
* Each child element contains Markdown content with specific {{Mustache\_Placeholders}}.
* For each element/placeholder, refer to its "Purpose" and "Guidance" sections in this document.
* The "Illustrative Example" (Section 5\) demonstrates a fully populated prompt.
* Your final output MUST be valid XML adhering to this specification.

**1\. Introduction: The Imperative for Structured AI Prompts**

The advent of sophisticated Artificial Intelligence (AI), particularly Large Language Models (LLMs), has opened new frontiers for deep research capabilities. These AI systems can process vast amounts of information, identify patterns, and generate human-like text, offering significant potential to accelerate and deepen research endeavors. However, eliciting high-quality, relevant, and precise outputs from these powerful tools is not always straightforward. The quality of AI-generated content is profoundly dependent on the quality of the input, commonly referred to as the "prompt." Effective prompt engineering is crucial for maximizing AI's potential. For complex tasks such as deep research, which demand nuance, context-awareness, and specific analytical approaches, generic or poorly constructed prompts often yield unsatisfactory results.

**2\. Rationale for a Structured Template Approach**

A structured template provides a standardized framework for users to articulate their research needs to a Deep Research AI. This approach ensures clarity, precision, reusability, and improved AI comprehension compared to free-form prompts, which can suffer from ambiguity, omission of critical details, and difficulty in reproduction. The selection of XML for structure and Markdown for content marries machine-interpretability with user-friendliness.

**3\. Core Principles of Effective AI Prompting for Deep Research**

The template is built upon these established best practices:

* **Clarity and Specificity:** Instructions should be direct and unambiguous.
* **Context Provision:** Supply relevant background information, domain-specific terminology, and the particular problem context.
* **Conciseness (Balanced with Detail):** Provide all essential information without overwhelming the model.
* **Iterative Refinement:** Prompting is often a conversational process.
* **Defining Output Expectations:** Clearly specify the desired format, tone, target audience, and length.
* **Leveraging Advanced Techniques:** The structure naturally guides users to provide input that supports advanced AI reasoning.

**4\. The XML/Markdown Prompt Template for Deep Research AI**

The proposed template employs a root XML element, \<DeepResearchPrompt\>, which encapsulates several key child elements. Each child element targets a specific aspect of the research query, containing Markdown-formatted placeholders for user input.

**Overall Structure**

\<DeepResearchPrompt\>
    \<ResearchObjective\>
        {{Main Research Question(s):}}

        {{Scope of the Research (e.g., specific timeframes, geographical boundaries, markets, demographics):}}

        {{Desired Outcomes or Deliverables (e.g., a ranked list, a comparative analysis, a strategic plan):}}
    \</ResearchObjective\>
    \<ContextualBackground\>
        {{Current situation or problem statement:}}

        {{Previous relevant experiences, learnings, or data:}}

        {{Wider context (e.g., market trends, technological advancements, societal shifts, historical background if relevant):}}
    \</ContextualBackground\>
    \<InputParameters\>
        {{Key Data Sources to Prioritize (e.g., specific databases, expert interviews, existing reports, preferred news outlets):}}

        {{Key Variables or Concepts to Investigate (e.g., specific product names, technologies, individuals, companies, policies):}}

        {{Specific preferences or constraints for data collection (e.g., preferred brands, price range, ethical considerations):}}

        {{Geographical Focus (if more specific than overall scope):}}

        {{Time Horizon for Data (e.g., data from last 5 years, most recent developments):}}
    \</InputParameters\>
    \<AnalyticalFramework\>
        {{Methodology or Approach (e.g., comparative analysis, trend identification, qualitative assessment, data aggregation):}}

        {{Key Evaluation Criteria (for assessing findings, e.g., relevance, reliability, actionability, cost-effectiveness):}}

        {{Assumptions to Test/Validate (if any):}}
    \</AnalyticalFramework\>
    \<OutputSpecifications\>
        {{Desired Output Format(s) (e.g., report, list, table, JSON, summary presentation):}}

        {{Target Audience (e.g., experts, general public, specific stakeholders):}}

        {{Tone and Style (e.g., formal, informal, academic, business-like, concise, detailed):}}

        {{Level of Detail (e.g., high-level overview, in-depth analysis, specific recommendations):}}

        {{Approximate Length or Key Sections Expected:}}
    \</OutputSpecifications\>
    \<IterativeRefinementNotes\>
        {{Initial Prompt Version (if applicable):}}

        {{Key Changes Made During Iteration:}}

        {{User Feedback Incorporated:}}
    \</IterativeRefinementNotes\>
\</DeepResearchPrompt\>

**For AI Agent Use: Hyperlocal Context-Gathering Framework**

* When a user's request triggers the "Hyperlocal Path," use this framework to guide your conversational questions.
* The goal is to gather the necessary details to populate the \<DeepResearchPrompt\> template for travel, event, or local planning.
* Ask questions naturally; do not just present this as a form.

**1\. The "Who" \- The Crew**

* **Core Question:** Who is this plan for?
* **Details to elicit:**
  * Number of people (adults, children, ages of children).
  * Key interests, hobbies, or roles of each person.
  * Physical limitations, accessibility needs, or major dislikes/allergies.
* **Purpose:** Informs recommendations for activities, food, and pace. Maps to \<ContextualBackground\> and \<KeyEvaluationCriteria\>.

**2\. The "When" \- The Timeline**

* **Core Question:** When is this happening?
* **Details to elicit:**
  * Specific dates (e.g., "July 14-20, 2026").
  * Time of day for specific events.
  * Season of the year.
* **Purpose:** Allows checking for holidays, business hours, seasonal availability, weather. Maps to \<Scope of the Research\> and \<Time Horizon for Data\>.

**3\. The "Where" \- The Anchor Points**

* **Core Question:** What is your home base or key location?
* **Details to elicit:**
  * Name of hotel and its address/neighborhood.
  * Address of an Airbnb or residence.
  * A key landmark or starting point for the plan.
* **Purpose:** Absolutely critical for "hyperlocal" queries (e.g., "closest...", "within walking distance"). Maps to \<Geographical Focus\>.

**4\. The "What" \- The Vibe & Constraints**

* **Core Question:** What is the overall goal or feeling of this plan?
* **Details to elicit:**
  * **Pace:** Relaxed, efficient, action-packed, kid-focused.
  * **Budget:** Frugal/budget, mid-range, luxury, "value-oriented."
  * **Key Goals:** What does a "successful" outcome look like? (e.g., "Find unique souvenirs," "Eat authentic local food," "Keep a 10-year-old engaged," "Replicate past positive travel experiences").
* **Purpose:** Defines the tone and evaluation criteria for the research. Maps to \<Desired Outcomes\>, \<KeyEvaluationCriteria\>, and \<Tone and Style\>.