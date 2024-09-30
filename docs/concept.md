# AI-PR-Reviewer - Concept Document

## Overview

**AI-PR-Reviewer** is a **GitHub Action** designed to automate and enhance the process of reviewing Pull Requests (PRs) by leveraging the capabilities of **OpenAI**. When triggered on a PR event, the action extracts the code differences (**diffs**) and submits them to OpenAI's model to generate a review, offering constructive feedback, suggestions, and identifying potential issues.

This action supports a customizable **configuration file** that allows teams to specify the prompt, tone, and criteria for the review process. Additionally, the action provides the option to attach other relevant documents (e.g., style guides, coding conventions) as contextual information to improve the quality of the AI-generated review. This ensures that the generated reviews align closely with the team's specific practices and requirements.

## Key Features

1. **Automated PR Review with OpenAI**:
    - Generates insightful and consistent reviews for every pull request by analyzing the code changes.
    - Saves time and enhances the quality of reviews by catching common issues or missed best practices.

2. **Customizable Review Prompts**:
    - Users can specify **custom prompts** in a configuration file to tailor the review tone, focus areas (e.g., code quality, security), and other review criteria.
    - Provides flexibility to adapt the review process based on the needs of individual projects or business units.

3. **Attach Contextual Documents**:
    - Ability to attach **style guides**, **coding standards**, or any relevant documentation to provide the AI with additional context when generating the review.
    - Ensures that the reviews align with the team's specific standards and avoid generalized suggestions.

4. **Integrated Workflow in GitHub Actions**:
    - Designed as a **GitHub Action**, seamlessly integrates with any GitHub repository to automatically trigger on pull request events.
    - Fully configurable to run at different stages, such as after code push or upon request for review.

## Use Cases

1. **Supplemental Code Reviews**:
    - Provides additional insights to manual reviewers by flagging issues or improvements they might miss.

2. **Quality Assurance for Open Source Projects**:
    - Ensures contributions meet a baseline level of quality without overburdening human maintainers.

3. **Accelerated Review Process for Busy Teams**:
    - Reduces the time developers spend on routine review tasks, enabling them to focus on more complex, strategic aspects of code quality.

## Architectural Overview

### Workflow Process
1. **Trigger Event**:
    - **AI-PR-Reviewer** is triggered by GitHub events such as **pull_request.opened**, **pull_request.updated**, or **pull_request.ready_for_review**.

2. **Configuration Loading**:
    - Loads configurations from a YAML or JSON file (`ai_pr_config.yml`). This file defines custom prompts, document attachments, and other parameters.

3. **Diff Extraction**:
    - The action extracts the **diff** (i.e., the code changes between the source and target branches) from the PR.

4. **Context Attachment**:
    - Additional documents (e.g., coding guidelines, style sheets) are attached to the payload to provide contextual understanding for OpenAI.

5. **Generate Review via OpenAI**:
    - The diff and context are sent to **OpenAI** using a pre-defined API prompt.
    - The AI generates a **review** that includes code suggestions, best practice recommendations, and identification of potential issues.

6. **Posting the Review**:
    - The generated review is posted back to the GitHub PR, either as a **single comment** or as **line-specific comments**.

### Example Workflow Configuration

The AI-PR-Reviewer is configured using a YAML file, which specifies the behavior of the action.

##### Example Workflow File (`.github/workflows/ai_pr_reviewer.yml`):

```yaml
name: AI-PR-Reviewer
on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  ai_review:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Run AI-PR-Reviewer
        uses: ai-tools/ai-pr-reviewer-action@v1
        with:
          config-file: '.github/ai_pr_config.yml'
          openai-api-key: ${{ secrets.OPENAI_API_KEY }}
```

### Example Configuration File (`.github/ai_pr_config.yml`):

```yaml
prompt: |
  You are an AI code reviewer. Please review the following diff for potential issues, such as bugs, coding standard violations, security vulnerabilities, or areas for optimization. Provide constructive feedback and suggest changes.

include_files:
  - 'docs/style-guide.md'
  - 'docs/security-guidelines.md'

options:
  tone: 'professional'
  level_of_detail: 'detailed'
  comment_format: 'inline'
  suggestions_enabled: true
  approval_suggestions: true
  maximum_comment_length: 2000
```

#### Configuration Breakdown

1. **Prompt**:
    - A customizable **prompt** that tells OpenAI how to approach the review. For example, it can be specific about identifying potential security issues or adhering to specific standards.

2. **Include Files**:
    - **include_files** is used to attach relevant documents to the prompt, providing OpenAI with additional context such as company coding standards or other project-related guidelines.

3. **Options**:
    - **tone**: Sets the tone of the generated review (e.g., `professional`, `casual`, `strict`).
    - **level_of_detail**: Controls the depth of the feedback (e.g., `detailed` or `summary`).
    - **comment_format**: Specifies whether comments should be posted inline or as a single comment on the PR.
    - **suggestions_enabled**: If `true`, OpenAI is asked to provide specific suggestions for code changes.
    - **approval_suggestions**: Allows the AI to make suggestions regarding whether the code is ready for approval.
    - **maximum_comment_length**: Defines the maximum length for each comment, useful for avoiding overly verbose feedback.

## Key Components

1. **GitHub Integration**:
    - The action is triggered by **GitHub PR events** and uses the GitHub API to interact with PRs.
    - **Checkout Action** (`actions/checkout`) is used to access the codebase for extracting diffs.

2. **OpenAI API Integration**:
    - Utilizes **OpenAI API** to process and generate reviews.
    - The **diff**, **prompt**, and **attachments** are packaged and sent as the request body.

3. **Custom Context Handling**:
    - The `include_files` option provides context to OpenAI, which helps it understand the specific rules, guidelines, or coding standards relevant to the project.

## Future Enhancements

1. **Advanced Contextual Understanding**:
    - Extend the capability to parse additional project files such as `package.json` or `.eslint` to automatically infer project-specific configurations and standards.

2. **Multi-Language Support**:
    - Add support for reviewing code written in various programming languages, adapting the prompts and rules based on language-specific standards and best practices.

3. **Feedback Loop and Learning**:
    - Implement a mechanism for developers to rate the quality of the AI review and use this feedback to improve the prompt or processing of future PR reviews.

4. **Security and Privacy Enhancements**:
    - Introduce security measures to anonymize sensitive parts of the code before sending it to OpenAI, ensuring compliance with privacy standards.

5. **Configurable Review Scenarios**:
    - Allow different prompts or behaviors for different types of PRs (e.g., feature branch vs. hotfix) or based on author profiles.

## Contribution Guidelines

**AI-PR-Reviewer** is an open-source project, and contributions are highly encouraged. Contributions can focus on:

- **Improving prompt engineering** to achieve better-quality reviews for different project types.
- **Adding integration tests** to verify that the GitHub action works seamlessly across different repository setups.
- **Extending configuration options** to improve customizability.
- **Implementing language detection** to provide better review prompts based on the type of code in the PR.

Refer to the `CONTRIBUTING.md` file in the repository for more detailed instructions on how to contribute.

## Conclusion

**AI-PR-Reviewer** provides a powerful, consistent, and scalable approach to reviewing pull requests by automating the process using OpenAI's language model. This GitHub Action integrates seamlessly into existing workflows and reduces the burden on human reviewers by offering preliminary suggestions and analysis, ensuring that quality and best practices are adhered to while accelerating the development process.

With its **customizable prompt** and **contextual document attachments**, AI-PR-Reviewer is highly adaptable and can align its reviews closely with team-specific practices. This ensures that the feedback generated is relevant and helpful, providing value to both small teams and large open-source projects.

