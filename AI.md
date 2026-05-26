**Status:** 🚧 - **Last Updated:** 24th May 2026

#### Table of Contents

- [MCP Server](#mcp-server)
- [Skills](#skills)
- [Tools](#tools)
- [Agent Team](#agent-team)
- [Prompt Engineering](#prompt-engineering)
  - [Cách hoạt động của LLM](#cách-hoạt-động-của-llm)
  - [Các nguyên tắc cơ bản khi viết prompt](#các-nguyên-tắc-cơ-bản-khi-viết-prompt)
  - [Cấu trúc của một prompt hiệu quả](#cấu-trúc-của-một-prompt-hiệu-quả)
  - [Kỹ thuật Prompt cơ bản (Text-based Prompting)](#kỹ-thuật-prompt-cơ-bản-text-based-prompting)
  - [System Prompting](#system-prompting)
  - [Zero Shot Prompting](#zero-shot-prompting)
  - [Role Prompting](#role-prompting)
  - [Few Shot Prompting](#few-shot-prompting)
  - [Instruction Prompting](#instruction-prompting)
- [5. Kỹ thuật tạo suy luận (Thought Generation)](#5-kỹ-thuật-tạo-suy-luận-thought-generation)
  - [Chain of Thought (CoT) Prompting](#chain-of-thought-cot-prompting)
  - [Self-Consistency Prompting](#self-consistency-prompting)
  - [Reflection Prompting](#reflection-prompting)
  - [Zero-Shot-CoT Prompting](#zero-shot-cot-prompting)
  - [Tree of Thought (ToT) Prompting](#tree-of-thought-tot-prompting)

### MCP Server

- [Figma MCP Server Setup Guide](https://developers.figma.com/docs/figma-mcp-server/) - A step-by-step guide to setting up a MCP (Model Control Plane) server for Figma, enabling developers to manage and deploy AI models effectively within the Figma ecosystem.

[↑ Back to top](#table-of-contents)

### Skills

- [Skills.sh](https://skills.sh/) - A platform that provides a comprehensive list of skills across various domains, including AI, programming, data science, and more. It helps users identify and learn the necessary skills for their desired career paths.
- [Open Design AI Skills](https://open-design.ai/skills/) - A resource that offers a curated list of skills related to open design and AI, providing insights and guidance for individuals looking to develop expertise in these areas.

[↑ Back to top](#table-of-contents)

### Tools

- [ChatGPT](https://chat.openai.com/), [Claude](https://www.anthropic.com/), [Gemini](https://gemini.com/), [Grok](https://grok.com/), [deepseek](https://deepseek.com/) - AI-powered search engines that provide advanced search capabilities and insights, allowing users to find relevant information quickly and efficiently.
- [Google AI Studio](https://ai.google/studio/), , [openrouter](https://openrouter.ai/) - Platforms that offer tools and resources for building and deploying AI models, providing users with the necessary infrastructure and support to create AI solutions.
- [kiwi](https://kiwi.com/) - An AI-powered travel search engine that helps users find and book flights, hotels, and other travel services, providing personalized recommendations and a seamless booking experience.
- [Opencode](https://opencode.ai/) - A platform that provides tools and resources for building and deploying AI models, offering a user-friendly interface for developers to create and manage their AI projects.
- [Ollama](https://ollama.com/) - A platform for building and deploying LLM agents, providing tools for creating intelligent agents that can perform a wide range of tasks using large language models.
- [Stitch](https://stitch.withgoogle.com/) - A tool that allows users to create and manage AI agents, providing a user-friendly interface for building and deploying AI solutions.
- [n8n](https://n8n.io/) - A workflow automation tool that allows users to connect various applications and services, enabling the automation of repetitive tasks and processes.
- [Hailuo AI](https://hailuoai.video/zh-Intl), [Piclumen](https://www.piclumen.com/) - AI-powered tools for generating images and videos, providing users with creative capabilities to produce high-quality visual content.

[↑ Back to top](#table-of-contents)

### Agent Team

- [BMAD](https://docs.bmad-method.org/) - A framework for building and managing AI agents, providing tools and best practices for creating effective and efficient AI systems.
- [obra/superpowers](https://github.com/obra/superpowers) - An agentic skills framework & software development methodology that works. `npx skills add obra/superpowers`
- [paperclip](https://github.com/paperclipai/paperclip) - Open-source orchestration for zero-human companies

[↑ Back to top](#table-of-contents)

### Prompt Engineering

- [Prompt Engineering Guide](https://drive.google.com/drive/folders/13QLaJbQYWfZNRfIzFolyz9or7ziTlSfx?usp=drive_link) - A comprehensive guide to prompt engineering, covering best practices, techniques, and examples for creating effective prompts for AI models.

[↑ Back to top](#table-of-contents)