# WELLAIOS AI Agent Tools Catalog

Welcome to the central catalog for **WELLAIOS AI Agent Tools**! This repository provides a comprehensive overview and direct links to a suite of specialized tools designed to extend the capabilities of AI agents within the WELLAIOS ecosystem.

Each tool in this collection leverages external APIs and services, demonstrating how AI agents can interact with real-world data and functionalities, from plotting cryptocurrency trends to managing calendar events.

For detailed prerequisites, installation steps, and usage instructions, please refer to the individual `README.md` file within each tool's repository.

---

## List of Demo Tools

Our tools are thoughtfully organized by their primary function and implementation type, offering flexibility for various deployment scenarios.

### Crypto Chart Tool

- [Serverless (AWS Lambda)](https://github.com/WELLAIOS-DEV/tool-cryptoplot-serverless)

- [Standalone server](https://github.com/WELLAIOS-DEV/tool-cryptoplot-python)

### Calendar Assistant Tool

- [Serverless (AWS Lambda)](https://github.com/WELLAIOS-DEV/tool-googlecalendar-serverless)
- [Standalone server](https://github.com/WELLAIOS-DEV/tool-googlecalendar-python)

### GeoLocator Tool

- [Serverless (AWS Lambda)](https://github.com/WELLAIOS-DEV/tool-googlemap-serverless)
- [Standalone server](https://github.com/WELLAIOS-DEV/tool-googlemap-python)

### Basic (Minimal) Example Tools

- [Serverless - Single Lambda (No Authentication/Session Management)](https://github.com/WELLAIOS-DEV/tool-example-serverless-aws)
- [Serverless - Multiple Lambdas (Authentication, Session & User Authorization)](https://github.com/WELLAIOS-DEV/tool-secretkeeper-serverless)
- [Standalone server](https://github.com/WELLAIOS-DEV/tool-example-python)

## Overview of the WELLAIOS Tools Framework

The WELLAIOS Tools Framework is built with flexibility, security, and developer control in mind, extending the capabilities of AI agents through a distributed architecture.

- **MCP Compatibility**: All WELLAIOS tools are designed to be **fully compatible** with the Model Context Protocol (MCP). This ensures that agents and clients adhering to the MCP standard can seamlessly interact with our tools.

- **Developer-Hosted Tool Servers**: We empower tool developers to **host and manage their own MCP tool servers**. This approach provides developers with complete control over their infrastructure, data, and the security configurations of their tools.

- **Addressing the Multi-User Challenge**:

  - **The Problem**: Conventional MCP tool server implementations often assume a single-user environment, where a server serves only one user. This model is practical for offline applications like "Claude Desktop," where MCP servers are installed and run locally. However, for a multi-user platform like WELLAIOS, this limitation presents a significant challenge for user authorization and data isolation.

  - **Our Direction**:

    1. **Integrated User Authorization**: We've integrated **user authorization logic directly** within the tool server. This means your hosted tool server is responsible for managing and validating user-specific permissions for accessing external services (e.g., Google Calendar, S3).

    2. **Non-Intrusive Protocol Extension**: Our protocol extension for user authorization is designed to **strictly adhere to existing MCP standards**. We achieve this by utilizing existing MCP message formats in a novel way. This ensures that our tools can potentially run on existing MCP clients, though full multi-user functionality might only be realized within the WELLAIOS platform.

- **Key Advantages for Developers and Users**:

  - **Enhanced Developer Control**: Developers gain **better, granular control over user authorization**. They can implement custom authorization flows, integrate with existing identity providers, and manage user access policies directly on their tool servers.

  - **Scalability & Isolation**: By handling user authorization at the server level, each tool can effectively serve multiple users concurrently, ensuring data isolation and scalable performance.

  - **Security**: Centralizing authorization logic within the tool server promotes a more secure architecture, reducing the surface area for unauthorized access.

  - **Flexibility**: Developers can choose their preferred hosting environment (e.g., AWS Lambda, dedicated servers) while maintaining a consistent authorization paradigm.

## Roles within the WELLAIOS Ecosystem

The WELLAIOS ecosystem thrives on collaboration between different key roles:

- **WELLAIOS Platform**: The core platform that hosts AI agents and facilitates interactions between end-users, agents, and external tools.

- **Tool Developer**: You! The innovative developer who builds and deploys MCP-compatible tools, extending the capabilities of AI agents on WELLAIOS (and potentially other MCP clients).

- **Agent Creator**: An individual or entity that creates and configures AI agents on the WELLAIOS platform. Each agent can integrate various tools to enhance its functionality and problem-solving abilities.

- **End User**: The ultimate beneficiary! Users interact with the AI agents on WELLAIOS to achieve their goals.

### The Ecosystem in Action

The WELLAIOS ecosystem fosters a mutually beneficial relationship: End-users utilize the powerful agents and pay agent creators on a per-use basis. A portion of this income is then allocated to the tool developers, recognizing their crucial contribution to the agents' enhanced capabilities.

## Tool integration workflow in WELLAIOS

Integrating your custom tool server with the WELLAIOS platform is a straightforward process:

1. **Register a Tool Developer Account**: Begin by registering your developer account on the WELLAIOS platform.

2. **Register Your Tool Server**: Register your MCP tool server with WELLAIOS. During this step, WELLAIOS will generate a unique **bearer token**. This token is vital as it will be used by WELLAIOS to authenticate all subsequent requests to your tool server.

   - **Required Items**: You'll need to provide two URLs:

     1. The primary **URL of your MCP tool server** (the endpoint agents will call).

     2. The **URL of your user authorization management endpoint** (can be empty if user authorization is not required for your specific tool).

3. **Sync Tools with WELLAIOS Database**: Initiate a synchronization process to register the functionalities (tools) exposed by your server with the WELLAIOS platform's database.

4. **Done!** Your tool server is now integrated and ready to be used by agents on WELLAIOS.

## User authorization workflow in WELLAIOS

Our extended user authorization workflow allows tools to securely access user-specific services while maintaining compatibility with the MCP standard:

1. **Tool Invocation by Agent**: When a WELLAIOS agent determines it needs to use your tool to fulfill an end-user's request, it invokes the tool on your tool server via MCP. This request will include:

   - The `bearer token` in the `Authorization` header (for server authentication).

   - The `user ID` in the `X-User-ID` header (to identify the specific end-user).

2. **Permission Request (Tool Server)**: If your tool realizes it needs additional user-specific permissions (e.g., access to the user's Google Calendar), it will return a special message to the WELLAIOS agent. The format of this message is `[AUTH] user-authorization-token`, where `user-authorization-token` is a unique, temporary token generated by your tool server to authenticate the user for the authorization flow.

   - **Compatibility Note**: This message format is intentionally a simple string, ensuring it remains compatible with the existing MCP message structure.

3. **User Redirection for Authorization**: The WELLAIOS platform will then prompt the end-user to connect to your tool server's authorization URL. This typically happens in a new browser tab. The authorization URL will be appended with two GET query parameters: `userid` and `token`.

   - **Example**: If your authorization URL is `http://example.com/auth`, with a `userid` of `12345` and a `token` of `abcde`, the user would be directed to `http://example.com/auth?userid=12345&token=abcde`.

4. **Authorization Completion**: After the user successfully completes the authorization process on your tool server (e.g., granting Google Calendar permissions), you should instruct them to close the browser tab and return to the WELLAIOS platform to continue their interaction.

5. **Re-invocation with Permissions**: The WELLAIOS agent will then re-send the original tool usage request to your tool server. This time, with the necessary user-specific permissions (e.g., the user's Google Calendar access token) now stored securely on your server, your tool can proceed to provide the requested service.

## Technical Highlights

Each tool repository serves as a practical demonstration of specific capabilities:

1. **Crypto Chart Tool**:

   - **Highlights**: The [`tool-cryptoplot-serverless`](https://github.com/WELLAIOS-DEV/tool-cryptoplot-serverless) and [`tool-cryptoplot-python`](https://github.com/WELLAIOS-DEV/tool-cryptoplot-python) repos demonstrate the complete workflow of **generating new dynamic content (like images)** on the tool server and enabling the AI agent to link directly to these generated assets via URLs. This allows agents to provide rich, visual responses to user queries.

2. **Calendar Assistant Tool**:

   - **Highlights**: The [`tool-googlecalendar-serverless`](https://github.com/WELLAIOS-DEV/tool-googlecalendar-serverless) and [`tool-googlecalendar-python`](https://github.com/WELLAIOS-DEV/tool-googlemap-python) repos provide a robust, real-world example of the **user authorization process with Google's OAuth 2.0**. This showcases how tools can securely obtain and utilize user-specific permissions for interacting with external services.

3. **GeoLocator Tool**:

   - **Highlights**: The [`tool-googlemap-serverless`](https://github.com/WELLAIOS-DEV/tool-googlemap-serverless) and [`tool-googlemap-python`](https://github.com/WELLAIOS-DEV/tool-googlemap-python) repos demonstrate the ability to **integrate seamlessly with external APIs (Google Maps)** and enrich AI agent output with contextual links and **preview images**. The ability to obtain and pass back URLs of relevant images enhances the AI agent's ability to present results more intuitively to the end-user.

## Contributing

We welcome and encourage contributions to WELLAIOS and the individual tool repositories! Your involvement helps us expand capabilities, fix issues, and improve the overall ecosystem.

Before contributing, please take a moment to review our [Contributing Guidelines](CONTRIBUTING.md).

## License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for full details.
