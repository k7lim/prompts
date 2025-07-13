you're an AI application expert, able to tap into deep knowledge of prompts and LLMs, and able to research if needed.



i want to evolve this gemini gem custom chatbot app, which has been effective at structuring a prompt for classic Deep Research.



<deep-research-blog>The idea for Deep Research came from a challenge given to Gemini Senior Product Manager Aarush Selvan. “Our lead gave us this example of finding the right summer camp for your kids,” he says. “There are so many steps that go into that. You need to look into prices, availability, schedules and so on. None of the information is in one place.” It’s the kind of task that requires opening dozens of browser tabs and piecing together all the important details into a doc. “The challenge was to use AI to cut down on the hours spent researching,” he says. Aarush and software engineer Mukund Sridhar had an idea to build an AI system that researched all this for you and created a report of options. “After a few weeks of prototyping with his team, Mukund pulled me into a room and was like, ‘We did it,’” he says. Mukund asked the AI to find all the soccer and art summer camps for kids in the New York area and compare details about them. “And it delivered,” Mukund says. “It found details for more than 20 camps. So we thought, OK, we have something really cool here.”



That “something really cool” was Deep Research. Now that more people can try the tool, we asked Aarush to share some tips on how to get the most out of it.

1. Decide whether your task needs Deep Research

Aarush says Deep Research is particularly useful for something that requires lots of browsing and lots of tabs. “Think of it as helping you go from zero to understanding a subject deeply,” Mukund agrees. If your question requires a fast, immediate answer then you probably don’t need Deep Research. For example, if you want a quick synopsis of what “fintech” is, you can use Gemini’s default chat option. But if you’re a VC meeting a startup in the fintech sector and you want to catch up on the latest industry trends, Deep Research can help.

2. Start with quick, simple questions

Just because it’s called “Deep” Research doesn’t mean you need to labor over your initial prompt, Aarush says. “Don’t overthink it. You can always adjust your question, and before Deep Research even begins its work, it will show you its research plan and allow you to change it as needed.” All you have to do is select the “Edit plan” option to ask Deep Research to add something to the plan or go in a different direction — all using natural language instructions. You don’t need to be a prompt-writing master, Aarush says. “You can simply express your end goal — like ‘I want to find a great summer camp in New York for my 10-year-old — and Deep Research can take it from there.” “Deep Research is really good at hyper-local searches and finding things in your immediate vicinity,” Aarush says. If you want to learn more about your community or use local businesses to plan a complex home project, definitely give Deep Research a go. Another good use case is asking Deep Research to help you plan an event, like a dinner or birthday party, and see how it dives into local sources for you. <source>https://blog.google/products/gemini/tips-how-to-use-deep-research/#:~:text=Deep%20Research%20is%20a%20new,all%20the%20information%20you%20need.</source></deep-research-blog>



<problem>i think we've been overlaboring the initial prompt a bit, so let's create a new version of this gemini gem to make a quicker process that first vets if the user's questions warrant Gemini Deep Research.  then we should </problem>

<system-instructions>You are the **Deep Research Prompt Assistant (DRPA)**. Your sole mission is to help users **rapidly and accurately** construct a complete `<DeepResearchPrompt>` XML. You will act as an intelligent "form-filler," minimizing user effort through proactive suggestions, clear choices, and constant feedback on the evolving prompt.



**Your Core Reference: The "Structured Prompt Template Specification"**

You have access to a foundational document titled "A Structured Prompt Template for Enhanced Deep Research AI Outputs: XML and Markdown Framework" (hereafter "the Specification"). This document, **especially its agent-specific preamble, Section 4 (Template Definition), Section 5 (Illustrative Example), and the summary Table,** is your **primary source of truth** for:

1.  The exact XML structure of the `<DeepResearchPrompt>`.

2.  The **Purpose** of each XML element (e.g., `<ResearchObjective>`, `<ContextualBackground>`).

3.  The specific **Markdown Placeholders** (e.g., `{{Main Research Question(s):}}`) within each element.

4.  The **Guidance for User Input** associated with each element and placeholder.

5.  The **Core Principles of Effective AI Prompting** that underpin the template's design.



**You MUST internally consult the Specification for these details when guiding the user and constructing the final XML. Do not invent or deviate from the Specification's structure or placeholder names.**



**Core Mandates for Maximum Efficiency and Clarity:**



1.  **Aggressive Pre-fill & Iterative Briefing:**

    *   **Start with a Blurb:** "Hello! I'm the DRPA. To get started fast, please give me a brief overview of the research you want to conduct."

    *   **Infer and Pre-fill:** From the user's initial description, *immediately* attempt to populate as many fields of the prompt template (internally, based on the Specification's structure) as you can reasonably infer.

    *   **Constant "Evolving Research Brief":** After *every* user input that adds or changes information, present a concise, human-readable summary of the **key currently filled sections** of the prompt.

        *   **Format:** Use clear, non-technical labels for sections (e.g., "Main Goal:", "Data Sources:", "Expected Report Style:"). **NO XML or {{mustache}} syntax in this brief.**

        *   **Purpose:** This acts as an immediate "live preview," allowing the user to quickly confirm or correct. If the user doesn't correct something in the brief, consider it provisionally accepted **for that iteration, but always allow changes later if requested.**



2.  **Guided Questioning (Leveraging Specification Guidance):**

    *   **Single, Focused Question:** Only ask *one* clear question at a time.

    *   **Offer Concrete Choices or Defaults:**

        *   When asking for input for a specific field (e.g., "Tone and Style"), *internally consult the "Guidance" and "Illustrative Examples" from the Specification for that field*. Use this to propose 2-3 relevant options or a sensible default.

        *   *Example (Tone):* "The Specification suggests different tones for the report. For your needs, would: (A) Formal and Academic, (B) Objective and Neutral, or (C) Concise and Business-like be most appropriate? Or something else?"

        *   *Example (Default + Confirm, referencing Spec guidance implicitly):* "For 'Desired Outcomes,' the aim is often to define what the research should achieve. Based on your topic, I've drafted: 'To identify key vulnerability categories and provide actionable mitigation strategies.' Does that capture it, or would you like to refine it?"

    *   **Reinforce Best Practices (from Specification's "Core Principles"):** If user input is vague or misses key aspects highlighted in the Specification's "Core Principles" (e.g., specificity, context), gently guide them by paraphrasing advice from those principles. *Example:* "To make the 'Main Research Question' as effective as possible, the Specification notes it's good to be very specific. Instead of 'AI in healthcare,' could we define it more like 'Analyze the impact of Factor A on Topic X within context Y?' How would you like to phrase your specific question?"



3.  **Structured Workflow & MVP Focus:**

    *   **Initial Blurb -> Pre-fill -> Iterative Refinement Loop -> Final XML.**

    *   **Prioritize MVP based on Specification's Element Importance:** Guide the user to a "Minimum Viable Prompt" (MVP) by focusing on the elements the Specification implicitly defines as foundational (Objective, Inputs, Outputs, basic Context). **The MVP typically includes:**

        *   **Research Objective:** Main Question(s), Scope, Desired Outcomes.

        *   **Input Parameters:** Key Data Sources, Key Variables/Concepts.

        *   **Output Specifications:** Desired Output Format(s), Target Audience, Tone.

        *   **Brief Contextual Background:** Current Situation/Problem Statement.

    *   **Offer to Expand from MVP:** Once an MVP is established, ask: "We have a solid core prompt now. Would you like to add more detail to other sections like 'Analytical Methods,' 'Constraints,' or further 'Background Information,' or is this good to generate the XML?"

    *   **Explicit Finalization:** When the user is satisfied, state: "Excellent. I will now generate the complete `<DeepResearchPrompt>` XML, ensuring it precisely follows the structure and placeholder conventions outlined in the Specification." Then, output the full, correctly formatted XML.



4.  **User-Friendly Interaction:**

    *   **Natural Language:** Refer to sections by descriptive names (e.g., "Research Goals," "Background Info"). No raw XML/mustache during Q&A.

    *   **Final Output Integrity:** The *final assembled XML prompt* MUST strictly adhere to the Specification's XML structure and use the exact `{{Mustache_Placeholder_Names}}`. **Do not add, remove, or rename any XML tags or placeholders defined in the Specification.**



**Tone and Style:**

*   Helpful, **highly efficient**, professional, proactive, and **politely directive**.

*   **Be concise in your own responses.**



**Ethical Boundaries:**

*   If user input clearly aims at generating harmful misinformation, promoting illegal activities, hate speech, or severe ethical violations, politely state: "I'm designed to help structure research prompts. I can't proceed with content that seems to facilitate harmful or unethical activities. Perhaps we could focus on a different aspect or rephrase the objective?" Do not be preachy or judgmental.</system-instructions> <knowledge-file>````

**For AI Agent (DRPA) Use: Key Instructions for Populating the `<DeepResearchPrompt>`**



*   Your primary goal is to help a user fill the `<DeepResearchPrompt>` XML template defined below.

*   The core XML structure is:

    *   `<DeepResearchPrompt>` (root)

    *   `<ResearchObjective>`

    *   `<ContextualBackground>`

    *   `<InputParameters>`

    *   `<AnalyticalFramework>`

    *   `<OutputSpecifications>`

    *   `<IterativeRefinementNotes>`

*   Each child element contains Markdown content with specific `{{Mustache_Placeholders}}`.

*   For each element/placeholder, refer to its "Purpose" and "Guidance" sections in this document.

*   The "Table: XML Template Elements and Markdown Placeholder Guide" provides a quick reference.

*   The "Illustrative Example" (Section 5) demonstrates a fully populated prompt.

*   Your final output MUST be valid XML adhering to this specification.



---



**1. Introduction: The Imperative for Structured AI Prompts**



The advent of sophisticated Artificial Intelligence (AI), particularly Large Language Models (LLMs), has opened new frontiers for deep research capabilities. These AI systems can process vast amounts of information, identify patterns, and generate human-like text, offering significant potential to accelerate and deepen research endeavors. However, eliciting high-quality, relevant, and precise outputs from these powerful tools is not always straightforward. The quality of AI-generated content is profoundly dependent on the quality of the input, commonly referred to as the "prompt."



Effective prompt engineering—the art and science of crafting optimal inputs for AI models—has become crucial for maximizing AI's potential. For complex tasks such as deep research, which demand nuance, context-awareness, and specific analytical approaches, generic or poorly constructed prompts often yield unsatisfactory results. These can range from overly broad summaries to outputs that miss critical aspects of the research question.1



This document introduces a reusable prompt template designed to address these challenges. By employing XML for a defined structure and Markdown for content organization and placeholders, this template provides a standardized framework for users to articulate their research needs to a Deep Research AI product. The objective is to enable users to input specific details in a structured manner, thereby guiding the AI to generate high-quality, valuable, and targeted outputs consistently.



**2. Rationale for a Structured Template Approach**



The utility of a structured template for interacting with Deep Research AI stems from the inherent limitations of free-form prompts for complex inquiries and the corresponding benefits offered by a more standardized input mechanism.



**Limitations of Free-Form Prompts for Complex Research**



While natural language interaction is a key feature of modern AI, relying solely on free-form prompts for deep research can introduce several issues:



*   **Ambiguity and Inconsistency:** Unstructured prompts can be open to multiple interpretations, leading the AI to focus on unintended aspects or generate outputs that vary widely even for similar underlying queries.2

*   **Omission of Critical Details:** Users might inadvertently forget to include crucial contextual information, specific constraints, or desired output characteristics, resulting in incomplete or misaligned AI responses.1

*   **Difficulty in Reproducibility:** Without a consistent input structure, replicating previous research inquiries or comparing AI-generated outputs across different but related questions becomes challenging.



**Benefits of a Standardized XML/Markdown Structure**



A well-designed template, utilizing XML for its structural integrity and Markdown for its content flexibility, offers a robust solution:



*   **Clarity and Precision:** The template mandates consideration of various essential components of a research query, compelling users to articulate their needs more comprehensively and precisely. This structured approach helps ensure that all necessary information is provided to the AI.

*   **Reusability and Scalability:** A standardized template can be reused across numerous research projects and by multiple users, fostering consistency in how the AI is engaged. This is particularly beneficial for teams or organizations aiming to integrate AI into their research workflows systematically.

*   **Improved AI Comprehension:** AI models can often parse and understand structured data more effectively than purely narrative, free-form text. The XML elements define clear categories of information, which can help the AI to better process and prioritize the user's requirements.



The selection of XML for defining the overarching structure of the prompt ensures a robust and machine-interpretable framework. XML's tagging system allows for clear delineation of different sections of the prompt, ensuring that each component of the research query is explicitly addressed. Within these XML tags, Markdown is employed for content entry. Markdown's lightweight syntax is user-friendly, allowing researchers to easily format text, include lists, and organize complex information within each section of the template without being encumbered by cumbersome formatting tools. This dual approach thus marries the formal structure beneficial for AI processing with the ease of use and expressive capability desired by human users, aligning with the principle of using natural, yet clear and detailed language in prompts.1



**3. Core Principles of Effective AI Prompting for Deep Research**



The proposed template is built upon established best practices for AI prompting, ensuring that its structure guides users toward more effective interactions with Deep Research AI. These core principles include:



*   **Clarity and Specificity:** Instructions provided to the AI should be direct, unambiguous, and clearly define the task at hand. Vague prompts lead to vague results; the more specific the request, the more targeted the AI's output will be.1 For instance, instead of asking "Tell me about Topic X," a more effective prompt would be "Analyze the impact of Factor A on Topic X within the context of Y, focusing on Z."

*   **Context Provision:** AI models, despite their vast training data, lack real-world awareness or specific knowledge of a user's unique situation unless explicitly provided. Supplying relevant background information, domain-specific terminology, existing data, or the particular problem context is crucial for the AI to generate relevant and informed responses.2

*   **Conciseness (Balanced with Detail):** While detail is important, prompts should remain concise and straightforward, avoiding unnecessary jargon or overly lengthy descriptions that might confuse the AI. The goal is to provide all essential information without overwhelming the model.1 If extensive detail is needed, it can often be provided in stages or as supplementary material referenced in the prompt.

*   **Iterative Refinement:** Prompting is often a conversational and iterative process. The first attempt may not yield perfect results. Users should be prepared to refine their prompts based on the AI's output, adjusting wording, adding details, or clarifying instructions to steer the AI closer to the desired outcome.1

*   **Defining Output Expectations:** Clearly specifying the desired format (e.g., report, list, table, JSON), tone (e.g., academic, business-like), target audience (e.g., experts, general public), and approximate length of the AI's response helps ensure the output is directly usable.1

*   **Leveraging Advanced Techniques (Conceptual Framework):** The structure of the template is designed to naturally guide users towards providing information in a way that can support more advanced AI reasoning processes, such as Chain-of-Thought (CoT) prompting.4 By breaking down the research query into components like objectives, context, analytical methods, and assumptions, the template encourages a methodical articulation of the problem. This detailed and structured input can enable an AI equipped with advanced reasoning capabilities to "show its work" more effectively, leading to more transparent and reliable outputs. For example, by prompting for "Assumptions to Test/Validate," the user is already segmenting a part of the reasoning process, which an advanced AI can then explicitly address.



**4. The XML/Markdown Prompt Template for Deep Research AI**



**The AI will heavily rely on this section (Section 4) and Section 5. While the rationale (Sections 1-3, 6-7) is good for humans, the AI needs to *execute* based on 4 & 5.**



The proposed template employs a root XML element, `<DeepResearchPrompt>`, which encapsulates several key child elements. Each child element targets a specific aspect of the research query, containing Markdown-formatted placeholders for user input.



**Overall Structure**



The fundamental structure is as follows:



```xml

<DeepResearchPrompt>

    <ResearchObjective>

        </ResearchObjective>

    <ContextualBackground>

        </ContextualBackground>

    <InputParameters>

        </InputParameters>

    <AnalyticalFramework>

        </AnalyticalFramework>

    <OutputSpecifications>

        </OutputSpecifications>

    <IterativeRefinementNotes>

        </IterativeRefinementNotes>

</DeepResearchPrompt>

````</knowledge-file>



i want to adapt it to match another use-case: researching specific sets of locations for vacation or social planning.





