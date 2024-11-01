import os
import base64
import requests
from dotenv import load_dotenv
from telegram import Update
from telegram.ext import Application, CommandHandler, MessageHandler, filters, ContextTypes

# Load environment variables from .env file
load_dotenv()
TELEGRAM_BOT_TOKEN = os.getenv("TELEGRAM_BOT_TOKEN")
GITHUB_TOKEN = os.getenv("GITHUB_TOKEN")
GITHUB_USERNAME = os.getenv("GITHUB_USERNAME")
REPO_NAME = os.getenv("REPO_NAME")

# Check if necessary tokens are available
if not all([TELEGRAM_BOT_TOKEN, GITHUB_TOKEN, GITHUB_USERNAME, REPO_NAME]):
    raise ValueError("Missing one or more environment variables.")

# GitHub request function with error handling
def github_request(method, url, data=None):
    headers = {
        "Authorization": f"token {GITHUB_TOKEN}",
        "Accept": "application/vnd.github.v3+json"
    }
    try:
        response = requests.request(method, url, headers=headers, json=data)
        response.raise_for_status()
        return response
    except requests.exceptions.RequestException as e:
        print(f"GitHub API request error: {e}")
        return None

# Function to create a new file
def create_file(filename, content):
    url = f'https://api.github.com/repos/{GITHUB_USERNAME}/{REPO_NAME}/contents/{filename}'
    content_base64 = base64.b64encode(content.encode("utf-8")).decode("utf-8")
    data = {
        "message": f"Create {filename}",
        "content": content_base64
    }
    response = github_request("PUT", url, data)
    return response and response.status_code in [200, 201]

# Function to edit a file
def edit_file(filename, new_content):
    url = f'https://api.github.com/repos/{GITHUB_USERNAME}/{REPO_NAME}/contents/{filename}'
    response = github_request("GET", url)
    if not response or response.status_code != 200:
        return False
    
    sha = response.json().get('sha')
    content_base64 = base64.b64encode(new_content.encode("utf-8")).decode("utf-8")
    data = {
        "message": f"Update {filename}",
        "content": content_base64,
        "sha": sha
    }
    update_response = github_request("PUT", url, data)
    return update_response and update_response.status_code in [200, 201]

# Function to view file content
def view_file(filename):
    url = f'https://api.github.com/repos/{GITHUB_USERNAME}/{REPO_NAME}/contents/{filename}'
    response = github_request("GET", url)
    if response and response.status_code == 200:
        content_base64 = response.json().get('content')
        content = base64.b64decode(content_base64).decode("utf-8")
        return content
    else:
        return None

# Function to delete a file
def delete_file(filename):
    url = f'https://api.github.com/repos/{GITHUB_USERNAME}/{REPO_NAME}/contents/{filename}'
    response = github_request("GET", url)
    if not response or response.status_code != 200:
        return False
    
    sha = response.json().get('sha')
    data = {
        "message": f"Delete {filename}",
        "sha": sha
    }
    delete_response = github_request("DELETE", url, data)
    return delete_response and delete_response.status_code == 200

# Function to list files
def list_files():
    url = f'https://api.github.com/repos/{GITHUB_USERNAME}/{REPO_NAME}/contents'
    response = github_request("GET", url)
    if response and response.status_code == 200:
        return [item['name'] for item in response.json()]
    else:
        return None

# Split text into parts if it exceeds Telegram's message limit
def split_text(text, max_length=4000):
    return [text[i:i + max_length] for i in range(0, len(text), max_length)]

# Send long messages by splitting them into parts
async def send_long_message(update, text):
    parts = split_text(text)
    for part in parts:
        await update.message.reply_text(part)