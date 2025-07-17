# Google Cloud Platform - Agent Development Kit
This is a folder dedicated to testing and creating an Agentic AI through Google Cloud Platform. To learn how to activate, develop, and create an AI agent, read below for a list of instructions.

## Starting your own ADK project
To begin an ADK project, it is recommended to isolate the ADK through creating a virtual environment. This can be done by typing in the terminal:
* `py -m venv .adk` or `python3 -m venv .adk`, depending on your version of Python.

## Activating and deactivating the virtual environment
To activate the venv, type in the terminal:
* Linux/macOS: `source .adk/bin/activate`
* Windows: `.adk/Scripts/activate`
  
Deactivating the venv is trivial; type in the terminal:
* `deactivate`

## Installing Google ADK through pip
To install the Google ADK:
* `pip install google-adk # Python3.9+`

To verify installation: (optional):
* `pip show google-adk` or `pip list | grep google-adk`

In case there are any issues with installing the Google ADK, the required pip installs are also located in `requirements.txt`.

## Managing files & directories
* A directory structure that must be maintained:
```
├── root directory
|   └── agent_function1
│       └── __init__.py (with import from agent)
│       └── agent.py
|   └── agent_function2
|       └── __init__.py (with import from agent)
│       └── agent.py
```

* .env files can be used to provide configurations for each agent (i.e. Gemini API or using Vertex AI projects)
* Some examples of .env statements:
```
GOOGLE_GENAI_USE_VERTEXAI=1
GOOGLE_CLOUD_PROJECT=very-best-project
GOOGLE_CLOUD_LOCATION=us-central1
MODEL='gemini-2.0-flash-exp'
```
* Basic agent coding:
  * An agent must have description and instructions
  * Some other parameters can be provided to customize the agent such as temperature with `generate_content_config`, tools, input or output schema, and callback functions

## Interacting with your agents
* There are four primary ways to interact with agents:
1. Web UI:
   * Good for visual interaction and monitoring agent behavior
   * Only used for local environment; not suitable for production
   * Run `adk web` from project folder to start local web server and user interface
2. CLI:
   * Good option for quick tasks, scripting, and automation
   * Use for terminal commands
   * Also only used for local environment; not suitable for production
   * Run `adk [run my_google_search_agent_name]` to start local CLI client
3. REST API:
   * Good option for integration with other apps, building services, and remote access to agent
   * Suitable for production environments
   * Run `adk api_server [run my_google_search_agent_name]` to start local API server using Flask on port 8000
4. Programmatic Interface:
   * Integrates ADK directly into Python applications or interactive notebooks (Jupyter, Colab, etc.)
   * Uses a Session and Runner
   * Suitable for production environments
   * Requires a 5 step process to use including setting up memory, creating a session and runner(s), and processing the event stream to get the final response.
     1. Set up Memory
     2. Create a new session
     3. Prepare content (user query) for agent
     4. Use a Runner to execute agent's logic
     5. Process the event stream to get the final response

## Creating a programmatic interface to interact with agents (Zoom on programmatic interface creation)
1. Set up Memory:
   ```
    # Create InMemory services for session and artifact management
    session_service = InMemorySessionService() # stores conversational history, internal state, other data related to specific interactions
    artifact_service = InMemoryArtifactService() # stores files, data generated or used by agent
   ```
2. Create a new session:
   ```
    # Create a new session
    session = session_service.create(app_name=AGENT_APP_NAME, user_id='user',)
    ```
3. Prepare content for agent:
    ```
    # Create a content object representing the user's query
    query = "Hi, how are you?"
    content = types.Content(role='user', parts=[types.Part(text=query)])
   ```
4. Use a Runner to execute agent's logic:
    ```
    # Create a runner object to manage the interaction with the agent
    runner = Runner(AGENT_APP_NAME, agent, artifact_service=artifact_service)

    # Run the interaction with the agent and get a stream of events
    events = runner.run(session_id=session.id, user_id="user001", new_message=content)
    ```
5. Process the event stream to get the final response
    ```
    # Loop through the events returned by the runner
    final_response = None
    for _, event in enumerate(events):
        is_final_response = event.is_final_response()
        if is_final_response:
            final_response = event.content.parts[0].text
    print(f'Final Response: {final_response}')
    ```

