# Local AI Setup: Ubuntu LTS with a GTX 1050 Ti

This guide covers the installation and management of **Ollama** using the specialized **Qwen2.5-Coder (1.5B)** model. The 1050 Ti is the reference card for this lightweight, high-performance setup.

## 🛡️ Safety & System Impact

* **System Integrity:** This installation does **not** modify your Linux kernel or overwrite your Nvidia drivers.
* **Resource Usage:** The 1.5B model uses ~1.1GB of VRAM, leaving ~2.9GB for your desktop and apps.
* **GPU Detection:** Ollama automatically uses CUDA cores if the **proprietary Nvidia drivers** (470+) are installed and active.

---

## 📥 Installation Steps

### 1. Verify GPU Drivers
Before starting, ensure your GPU is visible. Run:
```bash
nvidia-smi
```
**✅ What you should see:** A table showing "NVIDIA-SMI" and "Driver Version." Under "Product Name," it should list **GeForce GTX 1050 Ti**. If you see "Command not found," install drivers via *Software & Updates* first.

### 2. Install Ollama
The official script handles the binary installation and sets up the background service.
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

### 3. Download the Model
Pull the specific weights for the 1.5B Coder model.
```bash
ollama pull qwen2.5-coder:1.5b
```

---

## 🧠 Setting up the "ai" Command

To make your AI behave like an Ubuntu LTS expert using a dedicated alias file (the cleanest method for Ubuntu):

1.  **Open (or create) your aliases file:**
    ```bash
    nano ~/.bash_aliases
    ```
2.  **Paste this function into the file:**
    ```bash
    ai() {
      echo "🤖 Thinking..."
      ollama run qwen2.5-coder:1.5b "You are an Ubuntu LTS System Expert. Give concise terminal commands. Question: $*"
    }
    ```
3.  **Save and exit:** Press **Ctrl+O**, then **Enter**, then **Ctrl+X**.
4.  **Reload the configuration:**
    ```bash
    source ~/.bashrc
    ```

---

## 🔄 Updating Ollama & Models

To keep your setup current, follow these two steps:

### 1. Update the Ollama Software
Simply re-run the install script. It detects your existing installation and updates the binary to the latest version safely.
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

### 2. Update the Model Weights
Model creators often release "refined" versions of the weights. Run this to get the latest version of Qwen:
```bash
ollama pull qwen2.5-coder:1.5b
```

---

## 🛠️ Management & Monitoring

* **Monitor GPU:** Run `nvidia-smi` while the AI is thinking to see the VRAM usage (~1.1GB).
* **Service Logs:** If the AI isn't responding, check the logs: 
    ```bash
    journalctl -u ollama --no-pager | tail -n 20
    ```

---

## 🗑️ Uninstallation (Total Cleanup)

To completely remove Ollama and all downloaded models, run these steps in order:

### 1. Stop and Disable the Service
```bash
sudo systemctl stop ollama
sudo systemctl disable ollama
sudo rm /etc/systemd/system/ollama.service
sudo systemctl daemon-reload
```

### 2. Remove Binaries and System Users
```bash
sudo rm /usr/local/bin/ollama
sudo userdel ollama
sudo groupdel ollama
```

### 3. Wipe Model Data (The "Big" Files)
```bash
sudo rm -rf /usr/share/ollama
sudo rm -rf /var/lib/ollama
rm -rf ~/.ollama
```

### 4. Remove the "ai" Function
1.  Open your aliases file: `nano ~/.bash_aliases`
2.  Delete the `ai() { ... }` block.
3.  Save and exit, then run: `source ~/.bashrc`

---

## ⚠️ Disclaimer
This is provided "as is" without warranty of any kind. I am not responsible for any damage, data loss, or issues caused by the use of this information. **Use it at your own risk.**
