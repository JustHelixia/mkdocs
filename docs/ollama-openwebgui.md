# Ollama & Open Web-UI

## Introduction
With the latest AI innovations and Deepseek it has become easier to run AI-models on less powerfull hardware.  
I decided to give it a spin on my own desktop with deepseek-r1:14b, because the mini-PC I use for self hosting does not have a dedicated GPU.
The route I have taken is to install Ollama and Open WEbUI without using Docker instead I used the install script from Ollama and Python to run Open WebUI.  
  
## Installing Ollama
1. Download and install Ollama `curl -fsSL https://ollama.com/install.sh | sh` (The Linux way)  
If you run another OS you can check the [download page](https://ollama.com/download) to install Ollama.
2. Install your desired model(s)`ollama run MODEL`
You can find a list of [available models](https://ollama.com/search) on Ollama
  
## Bash script to start Ollama in the terminal
With the script below you can start Ollama and select your desired model.  
  
```bash
#!/bin/bash

while true; do
    # Get the list of installed models at each iteration
    installed_models=$(ollama list | awk '{print $1}')

    # Display available models and prompt for input
    clear && echo "
    $(date) - Choose a model to run with Ollama -

    The following models are installed:
    $installed_models

    You can type 'exit' at any time to quit.
    "

    read -p "Please select the name of the model you want to use: " MODEL_NAME

    # Trim any surrounding whitespace from the input
    MODEL_NAME=$(echo "$MODEL_NAME" | xargs)

    # Check if the user wants to exit
    if [ "$MODEL_NAME" == "exit" ]; then
        echo -e "\nExiting..."
        exit 0
    fi

    # Validate the entered model exists
    if ! echo "$installed_models" | grep -q "^$MODEL_NAME\$"; then
        echo -e "\nError: The model '$MODEL_NAME' does not exist."
        sleep 2  # Wait for 2 seconds before clearing the screen
    else
        # Start Ollama with the selected model and prompt to run again or exit
        echo -e "\nStarting Ollama with $MODEL_NAME..."
        ollama run "$MODEL_NAME"

        while true; do
            read -p "Would you like to run another model? (y/n) " yn
            case $yn in
                [Yy] ) break ;;
                [Nn] ) exit 0 ;;
                * ) echo "Please enter y or n." ;;
            esac
        done
    fi
done
```
  
## Locally run open-webui
1. Make sure you have Python 3.11 installed: `ls /usr/bin/python3*`
2. If not, install Python 3.11 `sudo dnf install pyhton3.11` (Fedora) or `sudo apt install python3.11` (Debian/Ubuntu)
3. Create a virtual Python environment `python3.11 -m venv venv`
4. Activate the virtual environment `venv\Scripts\activate`
5. Install Open WebUI (This step takes a while) `pip install open-webui`
6. Start the Open WebUI server `open-webui serve`
  
**TIP!**  
You can also use screen to run Open WebUI in a detached manner.  
  
1. Start the screen session `screen -S open-webui`
2. Run the command `open-webui serve`
3. Press CTRL+a, then d to detach the screen
4. Make sure the screen is still running `screen -ls`
  
Now you can start using Open WebUI by inserting one of the following addresses `localhost:8080` or `IPADDRESS:8080`.  
You can also use Docker to run this stack, but I choose to run it without Docker, because I otherwise have no access to my GPU.  
  
#### Resources:
- [Ollama](https://ollama.com/)
- [Ollama Github](https://github.com/ollama/ollama)
- [Open WebUI Docs](https://docs.openwebui.com/)
- [Open WebUi Github](https://github.com/open-webui/open-webui)