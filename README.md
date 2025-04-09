# hyper-kuzco-gensyn
Here is a 3-in-1 guide to run kuzco &amp; gensyn nodes on hyperbolic Labs 24/7


This guide explains how to rent a GPU on Hyperbolic Labs and use it to deploy nodes for Kuzco (a decentralized GPU network on Solana) and Gensyn (a layer-1 trustless protocol for deep learning compute). The entire process will be handled on the remote GPU terminal, which means your node keeps running 24/7—even when your local machine (e.g., your Mac) is off.


> Note: All commands here are to be run inside the Linux terminal of your rented GPU instance (accessed via SSH).


## Table of Contents

- [Step 1: Rent a GPU on Hyperbolic Labs](#step-1-rent-a-gpu-on-hyperbolic-labs)

- [Step 2: Run the Kuzco Node](#step-2-run-the-kuzco-node)

- [Step 3: Run the Gensyn Node](#step-3-run-the-gensyn-node)

- [Additional Notes](#additional-notes)

---

## Step 1: Rent a GPU on Hyperbolic Labs


### 1.1. Create Your Account & Fund It

1. Visit [Hyperbolic Labs](https://app.hyperbolic.xyz).

2. Sign up using your Email or Google account.

3. Verify your account through the confirmation email.

4. Click on **Deposit**, enter the deposit amount, and follow the checkout process (choose “Pay without link” if necessary).


### 1.2. Add Your SSH Public Key

1. On your local machine, open your terminal and generate an SSH key:
   ```bash
   ssh-keygen
   ```
   - Press Enter to use the default file location.
   - Enter a passphrase (or press Enter twice for no password).


2. Copy your public key:
   ```bash
   cat ~/.ssh/id_rsa.pub
   ```
   - The key starts with `ssh-ed...`.


3. In the Hyperbolic Labs dashboard, navigate to **Settings → SSH Public Key**.

4. Paste your key into the field and click **Save**.


### 1.3. Rent a GPU Instance

1. Go to the **Rent GPU** section in Hyperbolic Labs.

2. Select a GPU instance with the following recommendations:

   - **RAM:** Approximately 24GB

   - **GPU Options:** RTX 3090, RTX 4090, A100, or H100

3. Choose the **Ubuntu 22.04 with Nvidia CUDA** template.

4. Confirm your selection to launch the instance.

5. Copy the provided SSH command (e.g., `ssh -p 12345 username@your-instance-ip`) from the dashboard.

6. Open your terminal and paste the SSH command to connect to your GPU instance.  

   > **Remember:** You are now working on the remote GPU terminal provided by Hyperbolic Labs. Any commands executed here run on the cloud instance 24/7, independent of your local machine.

---

## Step 2: Run the Kuzco Node

Kuzco is a decentralized GPU network that rewards you in $KZO points for contributing your GPU power. Follow these steps to deploy a Kuzco worker.


### 2.1. Prepare the Environment

1. Update the system and install required tools:
   ```bash
   sudo apt update && sudo apt install -y curl screen
   ```

2. Create a directory for Kuzco and switch to it:
   ```bash
   mkdir -p ~/kuzco && cd ~/kuzco
   ```

3. Start a new screen session (this allows the process to run in the background):
   ```bash
   screen -S kuzco
   ```


### 2.2. Deploy the Kuzco Worker

1. Visit [https://inference.supply](https://inference.supply) and create your account.

2. Navigate to the **Workers** section and create a new worker.  

   - Select **CLI** as the install type.

3. Copy the commands provided on the website and paste them into your GPU terminal (inside the `screen` session) to install the CLI and launch your worker.

4. Once your Kuzco node is running (monitor the output), detach the screen by pressing `Ctrl + A` then `D`.

5. To reattach the screen later:
   ```bash
   screen -r kuzco
   ```

6. Verify your Kuzco node status via the Kuzco web interface after 5 minutes.

---

## Step 3: Run the Gensyn Node

Gensyn Protocol is designed for deep learning compute, rewarding you for offering compute capacity. The following instructions set up your Gensyn node.


### 3.1. Prepare the Environment

1. Install necessary dependencies:
   ```bash
   sudo apt update && sudo apt install -y python3 python3-venv python3-pip curl wget screen git lsof
   ```

2. (Optional) Install Yarn:
   ```bash
   curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
   echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
   sudo apt update && sudo apt install -y yarn
   ```

3. **Install Node.js (Choose one alternative method):**

   **Option A: Using NodeSource**
   ```bash
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt-get install -y nodejs
   ```
   
   **Or Option B: Using NVM (Node Version Manager)**
   ```bash
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
   # Activate NVM (or restart your terminal)
   export NVM_DIR="$HOME/.nvm"
   [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
   nvm install 18
   nvm use 18
   ```


### 3.2. Clone and Launch the Gensyn Node

1. Clone the RL Swarm repository for Gensyn:
   ```bash
   cd $HOME
   [ -d rl-swarm ] && rm -rf rl-swarm
   git clone https://github.com/0x5at/rl-swarm
   cd rl-swarm
   ```

   > **Note:** This guide uses the repository for RL Swarm, but you can replace it with your own if needed.
   
2. Create a new screen session for Gensyn:
   ```bash
   screen -S gensyn
   ```

3. Start the node:
   ```bash
   python3 -m venv .venv && . .venv/bin/activate && ./run_rl_swarm.sh
   ```

4. Follow the interactive prompts:

   - When asked "Would you like to connect to the Testnet? [Y/n]", type `Y` and press Enter.

   - When asked "Would you like to push models you train in the RL swarm to the Hugging Face Hub? [y/N]", type `N` and press Enter.

   - When prompted for an ngrok authtoken, visit [ngrok.com](https://ngrok.com) to create an account, retrieve your authtoken, and paste it into the terminal.

   - A URL ending in `http://ngrok-free.app` will be provided. Open it in your browser and sign in to complete authentication.

6. Once you see a welcome message (e.g., "Hello 🐈 [your username]"), your Gensyn node is live.

7. Detach the screen by pressing `Ctrl + A` then `D`.  
 
   To resume later:
   ```bash
   screen -r gensyn
   ```

8. Verify your node status by visiting the [Gensyn Dashboard](https://dashboard.gensyn.ai).

---

## Additional Notes

- **Running Remotely:**  

  All the commands and processes above are executed on your cloud GPU instance via SSH. Your local computer (Mac or PC) does not need to be on once the instance is running.

- **Screen Sessions:**  

  We use `screen` to allow the processes to continue running in the background. This is essential for keeping your nodes live even when you disconnect from SSH.

- **Monitoring:**  

  Regularly check your Kuzco and Gensyn dashboards to monitor node performance and resource utilization.

---

## Conclusion

By following this guide, you have:

- **Rented a GPU instance on Hyperbolic Labs** and accessed its remote Linux terminal.

- **Deployed a Kuzco node** to contribute GPU resources and earn rewards.

- **Launched a Gensyn node** for deep learning compute, using official methods for installing dependencies like Node.js.

Your nodes now run 24/7 on the Hyperbolic GPU instance, independent of your local machine.

Happy computing, and enjoy running your decentralized nodes!

---
