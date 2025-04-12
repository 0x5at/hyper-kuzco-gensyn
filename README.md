# hyper-kuzco-gensyn

This guide walks you through renting a GPU on Hyperbolic Labs and using it to run nodes for:

- **Kuzco** — A decentralized GPU network (Solana ecosystem)
- **Gensyn** — A trustless protocol for deep learning computation

---

## ✅ Step 1: Rent a GPU via Hyperbolic Labs

1. Go to [https://app.hyperbolic.xyz](https://app.hyperbolic.xyz)
2. Sign up with Google or Email and verify your account.
3. Click **Deposit** → enter the amount you want to deposit → **Pay Now**.
4. Select **Pay without link** to view deposit options and complete payment.

### 🔐 Add Your SSH Public Key

5. Open your terminal (Mac or VPS) and generate an SSH key:

```bash
ssh-keygen
```

> Just press enter to accept default file location and set a password (or press enter to skip password).

6. View your public key:

```bash
cat ~/.ssh/id_rsa.pub
```

7. Copy the full key starting with `ssh-ed25519` or `ssh-rsa`.

8. Go to **Settings** on Hyperbolic → paste your SSH key under **SSH Public Key** → Save.

---

## 🚀 Step 2: Connect to GPU and Run Kuzco Node

### 🎯 Rent a GPU

1. Go to the **Rent GPU** section.
2. Choose any with **24GB RAM** and CUDA like `RTX 3090`, `4090`, `A100`, or `H100`.
3. Select **Nvidia CUDA with Ubuntu 22.04**.
4. After instance starts, copy the provided `ssh` command.

### 🔗 Connect to Your GPU

Paste the `ssh` command into your terminal:

```bash
ssh <your-user>@<gpu-instance-ip>
```

Enter passcode if you created one earlier.

You are now inside your rented GPU.

---

### ⚙️ Install Dependencies

```bash
sudo apt update && sudo apt install -y curl screen
```

### 🛠️ Set Up Kuzco

```bash
mkdir -p ~/kuzco && cd ~/kuzco
screen -S kuzco
```

> Keep this screen session open for the next step.

1. Visit: [https://inference.supply](https://inference.supply)
2. Create an account
3. Go to **Workers** → create a worker → choose **CLI** install
4. Copy and paste the commands shown into the terminal screen

Once the worker is running:

- Detach the screen: `Ctrl + A + D`
- Reattach later with: `screen -r kuzco`

✅ Kuzco node is running in the background.

---

## 🧪 Step 3: Run Gensyn Node

### 🔁 Return to Home Directory First

```bash
cd $HOME
```

### 📦 Install Dependencies

```bash
sudo apt update && sudo apt install -y python3 python3-venv python3-pip curl wget screen git lsof
```

```bash
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update && sudo apt install -y yarn
```

### 🟢 Install Node.js + npm (Official NodeSource Script)

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
```

---

### 🧬 Clone and Run RL-Swarm

```bash
cd $HOME
[ -d rl-swarm ] && rm -rf rl-swarm
git clone https://github.com/0x5at/rl-swarm
cd rl-swarm
screen -S gensyn
```

### 🧠 Run the RL Swarm Script

```bash
python3 -m venv .venv
. .venv/bin/activate
./run_rl_swarm.sh
```

Respond to prompts as follows:

- `Connect to Testnet?` → **Y**

> 🛠️ If the terminal gets stuck waiting after this step, here's how to proceed:

### 🌐 Set Up Port Forwarding (If Terminal Freezes)

1. On your **local machine**, open a **new terminal window** (or tab) and run:

```bash
ssh -L 3000:localhost:3000 ubuntu@<your_server_ip> -p <port>
```

> Replace `ubuntu@<your_server_ip> -p <port>` with the actual SSH command Hyperbolic gave you (e.g., `ubuntu@12.34.56.78 -p 2222`).

2. Leave that terminal running.

3. Open [http://localhost:3000](http://localhost:3000) in your browser and complete the login.

4. Go back to the terminal where you ran `./run_rl_swarm.sh`. It should now continue.

5. After it starts running, you can **close the browser** and **the tunnel terminal**.

---

- `Push models to HuggingFace?` → **Y/N** (Optional)

**Create account in [HuggingFace](https://huggingface.co/)**

**Create an Access Token with `Write` permissions [here](https://huggingface.co/settings/tokens) and paste in prompt**

---

## ✅ Managing Screens

- **Detach current screen**: `Ctrl + A + D`
- **List active screens**: `screen -ls`
- **Reattach to a screen**: `screen -r <name>`

---

## 🔐 Gensyn `swarm.pem` Backup Guide

To safely back up your `swarm.pem` key (used to identify your Gensyn node), run the following command **on your GPU machine**:

```bash
mkdir -p ~/gensyn_backups && cp /home/ubuntu/rl-swarm/swarm.pem ~/gensyn_backups/swarm_$(date +%F_%H-%M-%S).pem
```

### ✅ What it does:
- Creates a backup folder (`~/gensyn_backups`) if it doesn’t exist.
- Copies your `swarm.pem` file with a timestamp to avoid overwriting previous backups.

### 📂 Example output:
```bash
~/gensyn_backups/swarm_2025-04-12_22-43-03.pem
```

### ♻️ To restore a backup:
If you ever need to restore a key, copy it back to the original path like this:

```bash
cp ~/gensyn_backups/swarm_YYYY-MM-DD_HH-MM-SS.pem /home/ubuntu/rl-swarm/swarm.pem
```

> Replace the filename with the exact timestamped backup you want to restore.

---

## 🧠 Useful Links

- Gensyn Dashboard: [https://dashboard.gensyn.ai](https://dashboard.gensyn.ai)
- Hyperbolic: [https://app.hyperbolic.xyz](https://app.hyperbolic.xyz)
- Kuzco CLI: [https://inference.supply](https://inference.supply)

---

## 🧩 Final Notes

- This setup allows you to run both Gensyn and Kuzco nodes 24/7 using rented GPU power from Hyperbolic.
- All screens will keep running in the background even if you disconnect from the GPU or shut your Mac.
