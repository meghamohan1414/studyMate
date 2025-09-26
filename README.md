# StudyMate: AI Driven Personalized Learning Companion

## Project Overview
### Core Idea: 
StudyMate is web application that dynamically tailors educational content - articles, quizzes, and flashcards - to each student's evolving proficiency of the subject. By integrating the course versioned in Git with advanced AI orchestration, embeddings and LLMs, StudyMate delivers personalized Learning paths that optimize engagement and retention.

### Challenge & Opportunity: 
Traditional Learning Management relies on static modules, leaving students either bored by redundancy or overwhelmed by gaps. StudyMate addresses this by:
1. Adaptive Content Delivery: Continually adjusting difficulty based on learner performance.
2. Dynamic Assessment: Generating bespoke quizzes and flashcards to reinforce concepts.
3. Data-Driven Insights: Tracking mastery trends to inform instructors and learners.

### Use Case & Workflow
### Target Users 
- K-12 Students & Educators: Teachers author curriculum in Git; students receive adaptive quizzes and flashcards.
  - Adaptive practice paths: AI-generated quizes and puzzles that automatically adjusts the diculty of questions based on the student's past performance.
  - Instant, tailored Feedback: Natural Language explanation for wrong answers, plus targeted flashcards generated on the fly to reinforce missed concepts.
  - Data-Driven Teaching Insights: Aggregate class performance dashboards highlight which standards or skills need extra attention, helping teachers group students for small-group tutoring/instructions.
- Corporate Training teams: Trainers version and deploy compliance modules; employees engage with micro-lessons matched to skill gaps.
  - Skill Gap Analysis:  AI Driven diagnostics that benchmark each employee's current competencies, then generate "just-in-time" micro learning modules tailored to their role.
  - Automated Content Versioning and compliance:** With Git-backed course repos and DryRun validations, trainers can iterate on modules, run Snyk scans on any code samples and deploy updates seamlessly.
  - Contextual Knowledge Retrieval: Employees can ask natural language questions and recieve structured, policy compliant procedures drawn from corporate playbooks stores and indexed in Pinecone.
- Self-Learners: Individuals follow AI-generated study plans in topics like coding or language learning.
  - Personalized Study Plans: Based on a brief skills survey, StudyMate uses LangChain orchestrated chains to generate a week-by-week roadmap, pulling relevant tutorials and flashcards from pinecone.
  - On-Demand Q&A and Explanations: Learners can pose free-form questions and OpenAI GPT-4 returns concise, example-driven answers.
  - Progress Tracking and Motivation: Automated reminders (via GitHub Actions + DryRun) nudge learners when it’s time to review flashcards or retake a quiz, while dashboards track streaks and mastery levels.
  - Rapid Prototyping of Projects: Using Cursor and CodeRabbit, self-learners can scaffold mini-projects (e.g., a simple web app) with AI-suggested code snippets, then test and iterate within the same environment.

### Workflow Steps:
1. Upload or Connect Documents:
   Instructors commit or push Markdown lessons, quizzes, and flashcards to the Git repo; students import external resources or upload files directly. A dashboard banner and toast confirm “Content Indexed: X items ready.”
- How it works:
   1. WebhookTrigger: GitHub Actions invokes FastAPI ingestion job.
   2. Content Parsing: FastAPI clones the repo or ingests uploads, parsing Markdown via markdown-it-py.
   3. Validation & Security: DryRun validates JSON schemas for quizzes/flashcards; Snyk scans embedded code for vulnerabilities.
   4. Embedding & Indexing: OpenAI embeddings API vectorizes content; Pinecone upserts vectors with metadata; Diamond exports metrics to Graphite.
2. Prompt & Generate Content:
   Educators select “Create Quiz” or “Generate Flashcards,” and students click “Start Quiz.” A prompt interface appears for any custom natural-language instructions.
- How it works:
   1. Prompt Engineering: LangChain wraps user input in templates; LangSmith logs prompt usage and performance.
   2. Profile Retrieval: LangChain fetches learner profiles (history, preferences) from the database.
   3. Initial AI Call: OpenAI GPT-4 generates seed questions or flashcard outlines based on prompts and profiles.
3. RAG-Powered Retrieval
   A loader message “Gathering relevant content…” appears before finalizing quiz or flashcard details.
- How it works:
   1. Vector Search: Pinecone queries return top-K semantically related lesson and Q&A chunks.
   2. Context Assembly: Retrieved text and metadata (topic, difficulty) are collated for AI enrichment.
4. Structured Generation:
   A personalized quiz, flashcard set, or study plan renders instantly as a table or interactive card deck.
- How it works:
   1. LLM Enrichment: LangChain feeds contexts and seed content into GPT-4 with instructions for JSON or Markdown outputs.
   2. DryRun or jsonschema validates the AI response against defined structures.
   3. Formatting: JSON is transformed into UI components or downloadable CSV/Markdown.
5. Review & Export:
   Options to “Download CSV/JSON,” “Send to Slack,” or “Publish to LMS” appear alongside content. Educators review analytics before approving.
- How it Works:
   1. Export Logging: Prometheus captures export events; Sentry records errors.
   2. Integration Hooks: Service posts data to webhooks (Slack, LMS APIs, BI tools) or generates pre-signed S3 URLs.
   3. Feedback Loop: Learner interactions and review approvals feed back into the profiling and prompt-optimization pipelines.
  
By following these five stages—Upload or Connect Documents, Prompt & Generate Content, RAG-Powered Retrieval, Structured Generation, and Review & Export—StudyMate automates and personalizes learning workflows from end to end.

### AI Features to Be Implemented
- **Prompt Engineering**
  - _Tools:_ OpenAI GPT-4, orchestrated by LangChain, monitored in LangSmith
  - _Why:_ Ensures consistent, pedagogically sound generations and enables analytics-driven prompt refinement.
- **Structured Outputs**  
  - _Tools:_ JSON schemas, validated by DryRun  
  - _Why:_ Guarantees predictable front-end rendering and reliable downstream analytics.
- **Retrieval-Augmented Generation (RAG)**  
  - _Tools:_ Pinecone (metrics via Diamond), OpenAI embeddings  
  - _Why:_ Semantic retrieval surfaces contextually relevant content, keeping AI outputs aligned with source material.
- **Evaluation Frameworks**  
  - _Tools:_ Synthetic test suites, human-in-the-loop reviews logged in LangSmith  
  - _Why:_ Quantifies quiz accuracy (precision/recall) and learning gains, guiding continuous improvement.
- **Observability Tools**  
  - _Tools:_ Prometheus/Grafana for system telemetry, LangSmith for prompt analytics, Sentry/Rollbar for error tracking  
  - _Why:_ Monitors performance, reliability, and prompt health to support SLAs and rapid debugging.

### Technical Approach
1. **CI/CD & Content Ingestion**  
   - **GitHub Actions** runs DryRun schema checks and Snyk scans on each commit.  
   - **FastAPI** microservices handle webhooks to clone repos or process uploads.

2. **Embedding & Vector Store**  
   - **OpenAI Embeddings API** vectorizes content chunks.  
   - **Pinecone** indexes and stores vectors; **Diamond** exports metrics to Graphite/Grafana.

3. **Orchestration & Prompting**  
   - **LangChain** pipelines sequence Pinecone retrieval and GPT-4 generation steps.  
   - **LangSmith** captures prompt usage, latency, and quality metrics for ongoing optimization.

4. **IDE & Developer Productivity**  
   - **Cursor** notebooks enable interactive prompt and chain prototyping.  
   - **CodeRabbit** suggests scaffold code for new workflows and React UI components.

5. **Deployment & Monitoring**  
   - Dockerized microservices (FastAPI, LangChain) run on Kubernetes, managed via Terraform.  
   - **Prometheus/Grafana** dashboards surface system, embedding, and prompt metrics; **Sentry/Rollbar** captures runtime exceptions.
  
### Example Prompts & Expected outputs
- **Quiz Generation Prompt**
Generate 5 multiple-choice questions covering the Pythagorean theorem. Output a JSON array with fields: question, options[], answer.
  - **Expected JSON**  
    [
      {
        "question": "In a right triangle with legs 3 and 4, what is the hypotenuse?",
        "options": ["5", "6", "7", "8"],
        "answer": "5"
      },
      …
    ]
- **Flashcard Generation Prompt**
Based on the student’s incorrect answers, retrieve related lesson segments on right-angle triangles and create 3 flashcards with term and definition in JSON.
  - **Expected JSON**
    [
      {
        "term": "Pythagorean Theorem",
        "definition": "In a right triangle, a² + b² = c², where c is the hypotenuse."
      },
      …
    ]

### Evaluation Strategy
- Automated Metrics: Precision/recall on quiz questions; relevance scoring for flashcards.
- Human Audits: Educator reviews logged in LangSmith to refine prompts.
- Learning Outcomes: Track improvements in student scores, completion rates, and time-on-task reductions.

### Observability Plan
- System Telemetry: quiz_generation_latency, flashcard_success_rate, and error rates in Prometheus/Grafana.
- Embedding Health: Pinecone query latency and usage via Diamond → Graphite.
- Prompt Analytics: Token usage, latency, and success rates in LangSmith.
- Error Tracking & Alerts: Sentry/Rollbar captures exceptions; Slack notifications trigger on SLA breaches (e.g., 95th-percentile latency > 2s).

StudyMate brings together LangSmith, Cursor, Diamond, CodeRabbit, Snyk, DryRun, Pinecone, and OpenAI into a cohesive, secure, and highly observable AI platform that personalizes learning at scale.


