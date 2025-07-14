# Complete Full-Stack Development Environment Setup Guide

## Prerequisites

Before starting, ensure you have:
- **Windows**: Windows 10 or later with administrator privileges
- **Mac**: macOS 10.15 or later 
- **Linux**: Ubuntu 20.04+ or equivalent distribution with sudo access
- Stable internet connection for downloads

---

## 1. Python 3.10+ Installation and Configuration

### Windows

**Step 1: Download Python**
1. Visit [python.org/downloads](https://python.org/downloads)
2. Download Python 3.10+ (latest stable version)
3. **Important**: Check "Add Python to PATH" during installation
4. Select "Install for all users" if you have admin rights

**Step 2: Verify Installation**
```cmd
python --version
pip --version
```

**Expected Output:**
```
Python 3.11.x
pip 23.x.x from C:\Program Files\Python311\Lib\site-packages\pip (python 3.11)
```

**Step 3: Configure Environment Variables (if PATH wasn't added)**
1. Press `Win + R`, type `sysdm.cpl`, press Enter
2. Click "Environment Variables"
3. Under "System variables", find and select "Path", click "Edit"
4. Add these paths (adjust Python version as needed):
   - `C:\Program Files\Python311\`
   - `C:\Program Files\Python311\Scripts\`

### Mac/Linux

**Step 1: Install Python (Mac)**
```bash
# Install Homebrew first (if not installed)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Python
brew install python@3.11
```

**Step 1: Install Python (Ubuntu/Debian)**
```bash
sudo apt update
sudo apt install python3.11 python3.11-pip python3.11-venv
```

**Step 2: Verify Installation**
```bash
python3 --version
pip3 --version
```

**Step 3: Create aliases (optional but recommended)**
Add to `~/.bashrc` or `~/.zshrc`:
```bash
alias python=python3
alias pip=pip3
```

**Troubleshooting:**
- If `python` command not found, use `python3`
- If pip installation fails, run: `python -m ensurepip --upgrade`

---

## 2. MySQL Community Edition Installation

### Windows

**Step 1: Download MySQL Installer**
1. Visit [MySQL Downloads](https://dev.mysql.com/downloads/installer/)
2. Download "MySQL Installer for Windows"
3. Choose "mysql-installer-community" (smaller web installer)

**Step 2: Install MySQL**
1. Run installer as administrator
2. Choose "Developer Default" setup type
3. Set root password (remember this!)
4. Configure MySQL as Windows Service (default)

**Step 3: Verify Installation**
```cmd
mysql --version
mysql -u root -p
```

**Expected Output:**
```
mysql  Ver 8.0.35 for Win64 on x86_64
Welcome to the MySQL monitor.  Commands end with ; or \g.
```

### Mac/Linux

**Mac Installation:**
```bash
brew install mysql
brew services start mysql

# Secure installation
mysql_secure_installation
```

**Ubuntu/Debian Installation:**
```bash
sudo apt update
sudo apt install mysql-server

# Start MySQL service
sudo systemctl start mysql
sudo systemctl enable mysql

# Secure installation
sudo mysql_secure_installation
```

**Step 3: Test Connection**
```bash
mysql --version
sudo mysql -u root -p
```

**Inside MySQL prompt, test:**
```sql
SHOW DATABASES;
CREATE DATABASE test_db;
DROP DATABASE test_db;
```

**Troubleshooting:**
- If connection denied: `sudo mysql_secure_installation`
- Check service status: `sudo systemctl status mysql` (Linux) or `brew services list | grep mysql` (Mac)

---

## 3. PostgreSQL Installation and Configuration

### Windows

**Step 1: Download PostgreSQL**
1. Visit [PostgreSQL Downloads](https://www.postgresql.org/download/windows/)
2. Download the installer for your Windows version
3. Run installer as administrator

**Step 2: Installation Configuration**
- Set password for postgres user (remember this!)
- Default port: 5432 (keep default)
- Default locale (keep default)

**Step 3: Verify Installation**
```cmd
psql --version
psql -U postgres -h localhost
```

### Mac/Linux

**Mac Installation:**
```bash
brew install postgresql
brew services start postgresql

# Create user (optional)
createuser --superuser postgres
```

**Ubuntu/Debian Installation:**
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib

# Start service
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

**Step 3: Configure and Test**
```bash
# Switch to postgres user (Linux)
sudo -u postgres psql

# Or connect directly (Mac/Linux)
psql -U postgres -h localhost
```

**Inside PostgreSQL prompt:**
```sql
\l
CREATE DATABASE test_db;
\c test_db;
DROP DATABASE test_db;
\q
```

**Expected Output:**
```
PostgreSQL 15.x on x86_64-pc-linux-gnu
postgres=# 
```

**Troubleshooting:**
- If `psql` not found, add to PATH: `/usr/lib/postgresql/15/bin`
- Connection issues: Check `sudo systemctl status postgresql`

---

## 4. MongoDB Community Edition Installation

### Windows

**Step 1: Download MongoDB**
1. Visit [MongoDB Downloads](https://www.mongodb.com/try/download/community)
2. Select Windows, Version 7.0+, Package: msi
3. Download and run installer

**Step 2: Installation Options**
- Choose "Complete" installation
- Install MongoDB as a Service (recommended)
- Install MongoDB Compass (GUI tool)

**Step 3: Verify Installation**
```cmd
mongod --version
mongo --version
```

**Step 4: Test Connection**
```cmd
mongosh
```

### Mac/Linux

**Mac Installation:**
```bash
# Add MongoDB repository
brew tap mongodb/brew
brew install mongodb-community

# Start MongoDB service
brew services start mongodb/brew/mongodb-community
```

**Ubuntu/Debian Installation:**
```bash
# Import public key
curl -fsSL https://pgp.mongodb.com/server-7.0.asc | sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg --dearmor

# Add repository
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

# Install MongoDB
sudo apt update
sudo apt install mongodb-org

# Start service
sudo systemctl start mongod
sudo systemctl enable mongod
```

**Step 3: Verify Installation**
```bash
mongod --version
mongosh --version
```

**Step 4: Test MongoDB**
```bash
mongosh
```

**Inside MongoDB shell:**
```javascript
show dbs
use test_db
db.test_collection.insertOne({name: "test"})
db.test_collection.find()
```

**Expected Output:**
```
test> db.test_collection.find()
[{ _id: ObjectId("..."), name: 'test' }]
```

**Troubleshooting:**
- Service not running: `sudo systemctl status mongod`
- Permission issues: Check `/var/log/mongodb/mongod.log`

---

## 5. Node.js LTS Installation

### Windows

**Step 1: Download Node.js**
1. Visit [nodejs.org](https://nodejs.org)
2. Download LTS version (recommended)
3. Run installer with default settings

**Step 2: Verify Installation**
```cmd
node --version
npm --version
npx --version
```

### Mac/Linux

**Mac Installation:**
```bash
# Using Homebrew
brew install node

# Or download from nodejs.org
```

**Ubuntu/Debian Installation:**
```bash
# Using NodeSource repository (recommended)
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs

# Verify build tools
sudo apt-get install -y build-essential
```

**Step 2: Verify Installation**
```bash
node --version
npm --version
npx --version
```

**Expected Output:**
```
v20.10.0
10.2.3
10.2.3
```

**Step 3: Update npm (optional)**
```bash
npm install -g npm@latest
```

**Troubleshooting:**
- Permission errors on global installs: Use `npm config set prefix ~/.npm-global` and add to PATH
- Or use Node Version Manager (nvm) for easier management

---

## 6. React.js Project with Vite

**Step 1: Create React Project**
```bash
npm create vite@latest my-react-app -- --template react
cd my-react-app
```

**Step 2: Install Dependencies**
```bash
npm install
```

**Step 3: Start Development Server**
```bash
npm run dev
```

**Expected Output:**
```
VITE v5.0.0  ready in 300 ms

➜  Local:   http://localhost:5173/
➜  Network: use --host to expose
➜  press h to show help
```

**Step 4: Verify in Browser**
- Open `http://localhost:5173/`
- You should see the Vite + React welcome page

**Step 5: Project Structure**
```
my-react-app/
├── public/
├── src/
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
├── package.json
└── vite.config.js
```

**Step 6: Make a Simple Change**
Edit `src/App.jsx`:
```jsx
function App() {
  return (
    <div>
      <h1>Hello, World!</h1>
      <p>My first React app with Vite!</p>
    </div>
  )
}

export default App
```

**Troubleshooting:**
- Port already in use: Vite will automatically try the next available port
- Module not found: Run `npm install` again

---

## 7. Django Project Setup

**Step 1: Create Project Directory**
```bash
mkdir my-django-app
cd my-django-app
```

**Step 2: Create Virtual Environment**
```bash
# Windows
python -m venv django_env
django_env\Scripts\activate

# Mac/Linux
python3 -m venv django_env
source django_env/bin/activate
```

**Step 3: Install Django**
```bash
pip install django
```

**Step 4: Create Django Project**
```bash
django-admin startproject myproject .
```

**Step 5: Run Development Server**
```bash
python manage.py migrate
python manage.py runserver
```

**Expected Output:**
```
System check identified no issues (0 silenced).
January 14, 2025 - 12:00:00
Django version 5.0.1, using settings 'myproject.settings'
Starting development server at http://127.0.0.1:8000/
```

**Step 6: Verify in Browser**
- Open `http://127.0.0.1:8000/`
- You should see "The install worked successfully! Congratulations!"

**Step 7: Create Superuser (optional)**
```bash
python manage.py createsuperuser
```

**Step 8: Project Structure**
```
my-django-app/
├── django_env/
├── myproject/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── manage.py
└── db.sqlite3
```

**Troubleshooting:**
- Virtual environment not activating: Check if you're in the right directory
- Port already in use: Use `python manage.py runserver 8001`

---

## 8. Flask Project Setup

**Step 1: Create Project Directory**
```bash
mkdir my-flask-app
cd my-flask-app
```

**Step 2: Create Virtual Environment**
```bash
# Windows
python -m venv flask_env
flask_env\Scripts\activate

# Mac/Linux
python3 -m venv flask_env
source flask_env/bin/activate
```

**Step 3: Install Flask**
```bash
pip install flask
```

**Step 4: Create app.py**
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return '<h1>Hello, World!</h1><p>My first Flask app!</p>'

@app.route('/about')
def about():
    return '<h1>About Page</h1><p>This is a Flask application.</p>'

if __name__ == '__main__':
    app.run(debug=True)
```

**Step 5: Run Flask Application**
```bash
python app.py
```

**Expected Output:**
```
 * Running on http://127.0.0.1:5000
 * Debug mode: on
 * Restarting with stat
 * Debugger is active!
```

**Step 6: Verify in Browser**
- Open `http://127.0.0.1:5000/`
- You should see "Hello, World!"
- Try `http://127.0.0.1:5000/about`

**Step 7: Alternative Run Method**
```bash
# Set environment variable and run
export FLASK_APP=app.py  # Linux/Mac
set FLASK_APP=app.py     # Windows
flask run
```

**Step 8: Project Structure**
```
my-flask-app/
├── flask_env/
├── app.py
└── requirements.txt (create with: pip freeze > requirements.txt)
```

**Troubleshooting:**
- Import error: Make sure virtual environment is activated
- Address already in use: Use `app.run(port=5001)`

---

## 9. FastAPI Project Setup

**Step 1: Create Project Directory**
```bash
mkdir my-fastapi-app
cd my-fastapi-app
```

**Step 2: Create Virtual Environment**
```bash
# Windows
python -m venv fastapi_env
fastapi_env\Scripts\activate

# Mac/Linux
python3 -m venv fastapi_env
source fastapi_env/bin/activate
```

**Step 3: Install FastAPI and Uvicorn**
```bash
pip install fastapi uvicorn[standard]
```

**Step 4: Create main.py**
```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    description: str = None
    price: float

@app.get("/")
async def read_root():
    return {"Hello": "World", "message": "Welcome to FastAPI!"}

@app.get("/items/{item_id}")
async def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "q": q}

@app.post("/items/")
async def create_item(item: Item):
    return {"item": item, "message": "Item created successfully!"}

@app.get("/health")
async def health_check():
    return {"status": "healthy", "version": "1.0.0"}
```

**Step 5: Run FastAPI Application**
```bash
uvicorn main:app --reload
```

**Expected Output:**
```
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [1234] using StatReload
INFO:     Started server process [5678]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```

**Step 6: Verify in Browser**
- Open `http://127.0.0.1:8000/`
- You should see JSON response: `{"Hello": "World", "message": "Welcome to FastAPI!"}`

**Step 7: Explore Interactive API Documentation**
- Open `http://127.0.0.1:8000/docs` (Swagger UI)
- Open `http://127.0.0.1:8000/redoc` (ReDoc)

**Step 8: Test API Endpoints**
```bash
# Test GET endpoint
curl http://127.0.0.1:8000/items/42?q=test

# Test POST endpoint
curl -X POST "http://127.0.0.1:8000/items/" \
     -H "Content-Type: application/json" \
     -d '{"name": "Laptop", "description": "Gaming laptop", "price": 999.99}'
```

**Step 9: Project Structure**
```
my-fastapi-app/
├── fastapi_env/
├── main.py
└── requirements.txt
```

**Troubleshooting:**
- Module not found: Ensure virtual environment is activated
- Port conflicts: Use `uvicorn main:app --port 8001 --reload`

---

## 10. Python Generative AI Project Setup

**Step 1: Create Project Directory**
```bash
mkdir my-ai-app
cd my-ai-app
```

**Step 2: Create Virtual Environment**
```bash
# Windows
python -m venv ai_env
ai_env\Scripts\activate

# Mac/Linux
python3 -m venv ai_env
source ai_env/bin/activate
```

**Step 3: Install AI Dependencies**
```bash
# Core AI libraries
pip install openai transformers torch langchain

# Additional utilities
pip install python-dotenv requests
```

**Step 4: Create .env File**
```bash
# Create .env file for API keys (don't commit to git!)
touch .env  # Linux/Mac
echo.> .env # Windows
```

Add to `.env`:
```
OPENAI_API_KEY=your_openai_api_key_here
```

**Step 5: Create ai_demo.py**
```python
import os
from dotenv import load_dotenv
import openai
from transformers import pipeline

# Load environment variables
load_dotenv()

class AIDemo:
    def __init__(self):
        # Initialize OpenAI (if API key available)
        self.openai_available = bool(os.getenv('OPENAI_API_KEY'))
        if self.openai_available:
            openai.api_key = os.getenv('OPENAI_API_KEY')
        
        # Initialize local model (Hugging Face)
        print("Loading local AI model...")
        self.local_generator = pipeline(
            "text-generation", 
            model="microsoft/DialoGPT-medium",
            tokenizer="microsoft/DialoGPT-medium"
        )
        print("Local model loaded successfully!")

    def chat_with_openai(self, prompt):
        """Chat using OpenAI API (requires API key)"""
        if not self.openai_available:
            return "OpenAI API key not available. Please add to .env file."
        
        try:
            response = openai.ChatCompletion.create(
                model="gpt-3.5-turbo",
                messages=[{"role": "user", "content": prompt}],
                max_tokens=150
            )
            return response.choices[0].message.content
        except Exception as e:
            return f"Error: {str(e)}"

    def chat_with_local_model(self, prompt):
        """Chat using local Hugging Face model"""
        try:
            response = self.local_generator(
                prompt, 
                max_length=100, 
                num_return_sequences=1,
                temperature=0.7
            )
            return response[0]['generated_text']
        except Exception as e:
            return f"Error: {str(e)}"

def main():
    print("🤖 Welcome to the AI Demo!")
    print("=" * 40)
    
    ai = AIDemo()
    
    while True:
        print("\nChoose an option:")
        print("1. Chat with OpenAI (requires API key)")
        print("2. Chat with local model")
        print("3. Exit")
        
        choice = input("\nEnter choice (1-3): ").strip()
        
        if choice == '3':
            print("Goodbye!")
            break
        
        if choice not in ['1', '2']:
            print("Invalid choice. Please try again.")
            continue
        
        prompt = input("\nEnter your message: ").strip()
        if not prompt:
            print("Please enter a message.")
            continue
        
        print("\n🤔 Thinking...")
        
        if choice == '1':
            response = ai.chat_with_openai(prompt)
        else:
            response = ai.chat_with_local_model(prompt)
        
        print(f"\n🤖 AI Response:")
        print("-" * 20)
        print(response)

if __name__ == "__main__":
    main()
```

**Step 6: Create Simple Text Generation Example**
```python
# simple_ai.py
from transformers import pipeline

def simple_text_generation():
    print("Loading AI model...")
    generator = pipeline('text-generation', model='gpt2')
    
    prompt = "The future of artificial intelligence is"
    
    print(f"Prompt: {prompt}")
    print("Generating response...")
    
    result = generator(prompt, max_length=50, num_return_sequences=1)
    
    print(f"\nAI Response: {result[0]['generated_text']}")

if __name__ == "__main__":
    simple_text_generation()
```

**Step 7: Run the Demos**
```bash
# Simple demo (no API key needed)
python simple_ai.py

# Full demo
python ai_demo.py
```

**Step 8: Expected Output (simple_ai.py)**
```
Loading AI model...
Prompt: The future of artificial intelligence is
Generating response...

AI Response: The future of artificial intelligence is bright and full of possibilities for innovation...
```

**Step 9: Project Structure**
```
my-ai-app/
├── ai_env/
├── .env
├── ai_demo.py
├── simple_ai.py
├── requirements.txt
└── .gitignore (add .env to this file!)
```

**Step 10: Create requirements.txt**
```bash
pip freeze > requirements.txt
```

**Troubleshooting:**
- Model download slow: First run downloads models, subsequent runs are faster
- Memory issues: Try smaller models like "gpt2" instead of larger ones
- CUDA errors: Models will fall back to CPU if CUDA unavailable

---

## Final Verification Checklist

Run these commands to verify everything is working:

### Python and Package Managers
```bash
python --version
pip --version
```

### Databases
```bash
mysql --version
psql --version
mongod --version
```

### Node.js
```bash
node --version
npm --version
```

### Project Verification
1. **React**: `cd my-react-app && npm run dev` → Check http://localhost:5173
2. **Django**: `cd my-django-app && python manage.py runserver` → Check http://127.0.0.1:8000
3. **Flask**: `cd my-flask-app && python app.py` → Check http://127.0.0.1:5000
4. **FastAPI**: `cd my-fastapi-app && uvicorn main:app --reload` → Check http://127.0.0.1:8000
5. **AI Demo**: `cd my-ai-app && python simple_ai.py`

---

## Common Issues and Solutions

### Virtual Environment Issues
- **Activation fails**: Check if you're in the correct directory
- **Commands not found**: Ensure virtual environment is activated (you should see `(env_name)` in terminal prompt)

### Port Conflicts
- If ports are busy, try alternative ports:
  - React: Vite auto-increments ports
  - Django: `python manage.py runserver 8001`
  - Flask: `app.run(port=5001)`
  - FastAPI: `uvicorn main:app --port 8001`

### Database Connection Issues
- **MySQL**: Check if service is running, verify credentials
- **PostgreSQL**: Ensure user has proper permissions
- **MongoDB**: Verify service status and check logs

### Permission Issues (Mac/Linux)
- Use `sudo` for system-wide installations
- For Node.js global packages, consider using nvm or configuring npm prefix

### Windows-Specific Issues
- **PATH not updated**: Restart terminal or add manually to system PATH
- **Permission denied**: Run terminal as administrator
- **Virtual environment activation**: Use `Scripts\activate` not `bin/activate`

---

## Next Steps

Now that you have a complete development environment:

1. **Learn Version Control**: Install Git and learn basic commands
2. **Code Editor**: Install VS Code with relevant extensions
3. **Database Tools**: Try MongoDB Compass, pgAdmin for PostgreSQL
4. **API Testing**: Install Postman or use built-in FastAPI docs
5. **Deployment**: Learn about Docker, cloud platforms (AWS, Heroku, Vercel)

Remember to always use virtual environments for Python projects and keep your dependencies updated!
