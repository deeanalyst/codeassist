
# CodeAssist - AI Programming Assistant

CodeAssist is a completely private and local AI coding assistant, developed by Gensyn. It helps you practice programming problems and train a novel assistant to help you code.

Unlike typical code assistants, CodeAssist writes directly in your editor as you work. Every keystroke - whether you type, fix, delete, or leave its output untouched - becomes a learning signal. Over time, it adapts to your habits and style, acting more like an apprentice learning from your craft than a tool following commands.

[Docs](https://docs.gensyn.ai/testnet/codeassist) | [Tutorial](https://docs.gensyn.ai/testnet/codeassist/using-codeassist) | [Leaderboard](https://dashboard.gensyn.ai/?application=CodeAssist)

## 🖥️ System Requirements

| Component              | Minimum Requirement | Recommended |
|-------------------------|---------------------|--------------|
| **Memory (RAM)**        | 12 GB               | 32 GB        |
| **Cores**                 | 4 | 8 |
| **Internet Connection** | Stable              | Stable       |
| **Storage**             | 30 GB available     | —            |
| **CPU**                 | Modern multi-core (Intel, AMD, or Apple Silicon) | — |

# Installation

Get started with installing CodeAssist.

## Install Dependencies

### Install Docker
```bash
sudo apt update -y && sudo apt upgrade -y
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y && sudo apt upgrade -y

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Test Docker
sudo docker run hello-world

sudo systemctl enable docker
sudo systemctl restart docker
```

## Python

Python is required to run the main script that handles your environment. We require a version no older than 3.10.

```bash
sudo apt install -y \
  python3 \
  python3-pip \
  python3-venv \
  python3-dev
python3 --version
```

## Install NPM & Node.js

```bash
# 1️⃣ Remove any existing node + npm (optional if you want a clean slate)
sudo apt remove -y nodejs npm

# 2️⃣ Update and install Node.js 22 from NodeSource
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs

# Install npm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
cat << 'EOF' >> ~/.bashrc
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
EOF
source ~/.bashrc
nvm --version

# 3️⃣ Verify installation
node -v   # should show v22.x
npm -v    # should show ~10.x (latest stable)
```

## UV

UV is required to manage the dependencies of the main script. It can be installed with the following steps:

### MacOS

```bash
brew install uv

### use the official installer
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Linux (or alternate MacOS install, for those without Brew)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source ~/.bashrc

### check version
uv --version
```

# Downloading Code Assist

To download the code, simply clone the repository:

```bash
git clone https://github.com/gensyn-ai/codeassist/
cd codeassist
```

# Running

To run CodeAssist, simply execute using any of the following command:

#### Rented VPS or Local Machine (Local MMachine highly recommended)
1. Open a screen (Rented Server else move to Step 2)
```bash
screen -S codeassist
```

2. Setup environment

```bash
export UV_HTTP_TIMEOUT=600   # 10 minutes
python3 -m venv .venv

source .venv/bin/activate
# if not worked, then:
. .venv/bin/activate
```

3. Install CPU pip packages

```bash
uv pip uninstall torch triton torchvision torchaudio
uv pip install --index-url https://download.pytorch.org/whl/cpu torch==2.9.0 torchvision torchaudio
uv pip install numpy
uv pip list

# confirm CPU-mode
python3 - << 'EOF'
import torch
print("Torch version:", torch.__version__)
print("CUDA available:", torch.cuda.is_available())
EOF
```

If your screen looks exactly as this image below, congrats!

<img width="460" height="152" alt="environment setup for CPU" src="https://github.com/user-attachments/assets/dd9c6142-c647-436c-971b-17c49daeeb50" />


4. Run python start script

```bash
uv run run.py
```

#### GPU Server
1. Open a screen (Rented Server else move to Step 2)
```bash
screen -S codeassist
```

2. Run python start script
```bash
uv run run.py
```
  To detach the screen, run `Ctrl + A + D`
  
3. Check logs (must detach from sccreen first)
```bash
cd && cd codeassist
# check run log which tells you if you did anything wrong
tail -fn 20 logs/run.log
```

## HuggingFace Token

To start CodeAssist, you will need to have a HuggingFace token. Follow [these instructions](https://huggingface.co/docs/hub/en/security-tokens) and generate a token with `Write` access.
> Now when you are prompted to use the HF token, when pasting it, it on't be visible. 
> In the event you feel you have made a mistake, run `Ctrl + C` then `nano .env` edit the token and restart codeassist with `uv run run.py`.

Else skip to [Web UI Login to Gensyn Account]()

## Manual Docker Images PUll
```bash
docker pull ollama/ollama:0.11.10 2>&1 | tail -20
docker pull gensynai/codeassist-policy-model:main 2>&1 | tail -20
```

If you decided to go the manual route. Now continue here:
Start codeassist again
   ```bash
   uv run run.py --no-pull
   ```
> You won't be prompted to input your HF_Token if this is not the first time running start command.

## Web UI Login to Gensyn Account

After the script is running, your browser should open automatically when `container setup` is at `3/5` but if it doesn't, open a window and go to [localhost:3000](http://localhost:3000) to open CodeAssist. 
`N/B:` You will only get to this point if the `Ollama 0.11.10` model suuccessfully downloaded, pulled and started successfully. You won't get the logs at this point, but if you notice it takes longer than 5 minutes, terminate again using `Ctrl + C` and [pull images manually](https://github.com/deeanalyst/codeassist/tree/main#manual-docker-images-pull).

### Login on Local Machine

You should see https://localhost:3000 auto pop up or load in your default browser.

### Login on VPS or Rented GPU Servers

#### Option 1: Use NGROK (More Stable, Recommended)

1. Signup at [Ngrok Dashboard](https://dashboard.ngrok.com/) and complete the onboarding steps.
   
2. Install `ngrok` on your server

```bash
curl -sSL https://ngrok-agent.s3.amazonaws.com/ngrok.asc \
  | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null \
  && echo "deb https://ngrok-agent.s3.amazonaws.com bookworm main" \
  | sudo tee /etc/apt/sources.list.d/ngrok.list \
  && sudo apt update \
  && sudo apt install ngrok
```
   
3. Save your Auth token
   ```bash
   # Looks like this and you will get it if you successfully login to the dashboard of ngrok.
   ngrok config add-authtoken 31q0..........................3zJcM
   ```
   
4. Create screen for ngrok
   ```bash
   screen -S ngrok
   ```
   
5. Forward the port to a web browser on local system
   ```bash
   ngrok http 3000
   ```
   
    <img width="402.5" height="258" alt="image" src="https://github.com/user-attachments/assets/bcefb38e-7363-457a-a322-766b7e451606" />
    
    > If you get to this image, just Hold `Ctrl` on your keyboard and left-click at the same time to load it in your browser, or copy the link to your local machine browser.

#### Option 2: Use LT

1. Open a new terminal
   
2. Install LocalTunnel:
   ```bash
   sudo npm install -g localtunnel
   ```

3. Get password
   ```bash
   curl https://loca.lt/mytunnelpassword
   # The password is actually your VPS IP
   ```

4. Get forwarded URL
   ```bash
   lt --port 3000
   ```
   Copy the link that gets outputted like in the image below or hold down `Ctrl` while left-clicking the url. The `VPS IP`  will be your password when you load the URL

   <img width="646" height="207" alt="image" src="https://github.com/user-attachments/assets/210c691b-d463-4b39-abf4-33286abe42ac" />

### GPU Setup with [👉 Octa: Create an Account Here 👈](https://octa.space?ref=rB0IMePn4cZ)
  > Fund your account with $5 - $15.
  > Also note that to save on cost, after creating these GPU sessions and training for as much as you can, do well to destroy the session till you want to test CodeAssist again.

1. Setup CodeAssist on Octa using this [Guide](https://docs.octa.space/gensyn-codeassist)

2. If you have successfully set it up on Octa Space, now you werre not told in that guide how to properly run the train trigger.
   If you outrightly just do `Ctrl + C` on the Web Terminal, you will end up with the `Error: Keyboard Interrupt`.
   To do this right, do the following:

   1. Realize that the Web Terminal is a `tmux` session, run this:
      ```bash
      Ctrl + B # On your keyboard
      ```
   2. Then press:
      ```bash
      : # i.e Shift + Semi-Colon/Colon Button on your Keyboard
      ```
   3. Copy the below command
      ```bash
      send-keys C-c
      ```
   4. Right-click anywhere within the browser terminal and as in the image below press the `Paste` button from the pop-up, this should automatically trigger training on your Terminal
      
      <img width="480" height="260.25" alt="codeassist4" src="https://github.com/user-attachments/assets/65da883a-fb1b-4bec-8a65-92b1fdce5b0f" />

      And finally if your model trains right you should get an image like the below of successful training, transactions sent and model push to Huggingface.
    
      <img width="400" height="206.75" alt="image" src="https://github.com/user-attachments/assets/a811ae8e-9a01-4231-a0e2-636a077509d0" />

   To restart another training session when the last line of your terminal looks similar to the immediate screenshot above.

   Run `Ctrl + Shift + V` to paste the below command.
   
   ```bash
   cd codeassist
   source .venv/bin/activate
   source ~/.bashrc
   uv run run.py
   ```

When the web UI loads, you'll see a login modal where you can log in with email (which sends a one-time passcode) or with Google. After logging in for the first time, your local credentials will be stored in `persistent-data/auth/userKeyMap.json`.

Once logged in, you can select Easy, Medium, or Hard problems from the sidebar. CodeAssist will begin recording an episode. Every click, keystroke, edit, or deletion is logged as training feedback.

When you stop typing, CodeAssist takes initiative. It writes directly into your file without any pop-ups or confirmations. Whether you accept, modify, or remove its edits, each interaction contributes to the model’s understanding of your preferences.

### Tips & Tricks

- Use `Shift+Space` or click the Pause Assistant button to temporarily stop the assistant. The first keystroke after pausing will unpause it.
- Keep your cursor near the section you're working on, as CodeAssist inserts code relative to your cursor position.
- When the assistant produces a "No-Op" (does nothing), it's waiting for you. This is intentional and signals it's your turn to act.

## Training

CodeAssist continuously records your interactions while the web UI is running. To complete an episode and train your model, press `Ctrl+C` in the terminal where CodeAssist is running.

You do not need to successfully solve a LeetCode problem to train the model. You can stop recording the episode by leaving the CodeAssist web UI, returning to the terminal CodeAssist is running in, and using the `Ctrl+C` command to start training.

During training, CodeAssist will:
- Compare your edits to the assistant's actions
- Calculate rewards and penalties based on your interactions
- Update your local model checkpoint
- Store new model weights under `persistent-data/trainer/models`
- Upload your trained model to Hugging Face (if a valid token is provided)

After training completes (which takes a few minutes depending on your system), you can restart CodeAssist to use your updated model trained on your most recent episode.

### Best Practices

- **Be patient**: The assistant watches your typing and timing. Working too quickly or aggressively correcting can neutralize training efficacy.
- **Treat it as a collaborator**: Let it naturally interject code and keep useful code around briefly before editing or removing.
- **Don't delete everything instantly**: If you delete everything it writes right away, you're teaching it to stop acting altogether.
- **Record multiple varied problems**: Diversify its learning signals by working on different problems.
- **Expect gradual improvement**: Early episodes may feel inconsistent. Improvement becomes clearer after 4-5 episodes of training.

# Troubleshooting

## Exception: Container <container-name> is unhealthy.

This occurs when a container fails to boot. You can view the logs by running `docker logs <container-name>`. Please review and upload the logs when creating a new issue.

## Error connecting to Docker daemon

```
2025-09-04 15:03:47,975 - ERROR - Error connecting to Docker daemon: Error while fetching server API version: ('Connection aborted.', FileNotFoundError(2, 'No such file or directory'))
2025-09-04 15:03:47,976 - ERROR - Please ensure Docker is installed and running.
```

If you see lines like the above, it means Docker is not running, or you do not have permission to connect to Docker. Make sure Docker is properly installed on your system and running.

#### Quick Fix
```bash
sudo systemctl restart docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
# then you close your terminal reload it and `cd codeassist` and continue from where you left off.
```

## Bind for 0.0.0.0:3000 failed: port is already allocated

This occurs when there is already a Docker container running that uses port 3000. If you're running RL Swarm on the same machine, you will have to stop RL Swarm to use CodeAssist.

# Contributing

We welcome contributions!

## Pre-Commit Hook

We use a pre-commit hook to run linting on the repository before committing. You can install `pre-commit` as described below:

### Installing `pre-commit`

To install `pre-commit`, simply run the below command

```bash
pip install pre-commit
```

### Setting up `pre-commit`

To set up `pre-commit`, run this command

```bash
pre-commit install
```

## Formatting

This repository is formatted with `ruff format`. Any commits which do not meet Ruff's lint checks will have GitHub actions fail, and will need to be fixed before merging.

# License

CodeAssist is released under the (MIT) license.
