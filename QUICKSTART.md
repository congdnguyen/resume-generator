# Resume Generator - Quick Start Guide

## Getting Started in 5 Minutes

### Step 1: Start the Services

```bash
cd G:\tvpine\randomtask\publish
docker compose up -d
```

Wait ~30 seconds for both services to initialize.

### Step 2: Access the Web UI

Open your browser and navigate to:
```
http://localhost:8079
```

### Step 3: Configure Your AI Provider

#### Option A: Local Model (Ollama)

1. Ensure Ollama is running on your network
2. Enter your Ollama URL (default: `http://localhost:11434`)
3. Leave API key blank (or enter any dummy value)
4. Select a model from the dropdown

#### Option B: Cloud Model

1. Enter your API base URL (e.g., `https://api.openai.com/v1`)
2. Enter your API key
3. Click "Enter API key" to fetch available models
4. Select your preferred model

### Step 4: Generate Your Resume

1. **Paste Job Description**: Enter the job description you're applying for
2. **Paste Your Resume**: Paste your current resume (any format works!)
3. **Select Theme**: Choose from 9 professional themes
4. **Enable Tailoring**: Keep "Tailor to job" checked for AI optimization
5. **Click "Generate Resume"**

### Step 5: Download Your Resume

Once generation completes:
- **Preview**: View your tailored resume in the right panel
- **Download**: Click any of the download buttons:
  - 📄 PDF (primary format)
  - 🖼️ PNG (image)
  - 📝 MD (markdown)
  - 🌐 HTML (web)
  - 📔 Typst (typesetting)

---

## Common Scenarios

### Scenario 1: First Time Setup (Local Model)

```bash
# 1. Ensure Ollama is running
# (Install from https://ollama.com if needed)

# 2. Pull a model
ollama pull qwen2.5:3b

# 3. Start the stack
docker compose up -d

# 4. Access web UI
open http://localhost:8079
```

### Scenario 2: Using OpenAI API

1. Get your OpenAI API key from https://platform.openai.com
2. In the web UI:
   - Base URL: `https://api.openai.com/v1`
   - API Key: `sk-...` (your key)
   - Model: Select from dropdown (e.g., `gpt-4o`)
3. Generate your resume

### Scenario 3: Using Anthropic (Claude)

1. Get your Anthropic API key from https://console.anthropic.com
2. In the web UI:
   - Base URL: `https://api.anthropic.com/v1`
   - API Key: `sk-ant-...` (your key)
   - Model: Select from dropdown (e.g., `claude-3-sonnet-20240229`)
3. Generate your resume

---

## Tips for Best Results

### Resume Text Tips

✅ **Include:**
- Complete work experience with dates
- Education history
- Skills section
- Contact information
- Projects (if applicable)

❌ **The system will automatically remove:**
- Duplicate sections
- AI-generated commentary
- Placeholder text like "(Date of Employment)"
- Multiple versions of the same content

### Job Description Tips

✅ **Best practices:**
- Paste the full job description
- Include required skills and qualifications
- Add company information if relevant
- Include specific responsibilities

### Tailoring Tips

**Enable tailoring when:**
- You want the resume optimized for a specific job
- The job description has clear skill requirements
- You're applying to a competitive position

**Disable tailoring when:**
- You want your original wording preserved
- You're creating a general-purpose resume
- The job description is very brief

---

## Troubleshooting

### "Could not parse resume"

**Cause:** The LLM couldn't extract a name or experience from your resume.

**Fix:**
1. Ensure your resume contains:
   - A clear name
   - At least one work experience entry
   - Reasonable date formats

### "Timed out"

**Cause:** The LLM took too long to respond.

**Fix:**
1. Try a smaller model (e.g., `qwen2.5:3b` instead of `qwen2.5:7b`)
2. Increase timeout in environment variables:
   ```bash
   OLLAMA_TIMEOUT=600  # 10 minutes
   ```
3. For cloud APIs, check your rate limits

### "PDF generation failed"

**Cause:** RenderCV encountered invalid data.

**Fix:**
1. Check the error message for specific fields
2. Common issues:
   - Invalid phone format
   - Invalid email format
   - Missing required fields
3. The system will automatically retry with problematic fields removed

### "No models found"

**For Ollama:**
```bash
# Check Ollama is running
ollama list

# If not running, start it
ollama serve
```

**For Cloud APIs:**
1. Verify API key is correct
2. Check the model ID is valid for your provider
3. Ensure you have access to the model

---

## Environment Variables

You can customize the behavior by setting environment variables:

```bash
# docker-compose.yml or .env file
OLLAMA_BASE_URL=http://localhost:11434
MODEL_NAME=qwen2.5:3b
NUM_CTX=8192
OLLAMA_TIMEOUT=300
CLOUD_TIMEOUT=120
```

---

## API Usage

### cURL Example

```bash
curl -X POST http://localhost:8077/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "job_description": "Senior Software Engineer with 5+ years experience in Python and AWS.",
    "resume": "John Doe\njohn@example.com\n\nExperience:\nSoftware Engineer at Tech Corp (2020-2024)\n- Developed Python applications\n- Managed AWS infrastructure",
    "theme": "ink",
    "tailor": true,
    "base_url": "http://localhost:11434",
    "model": "qwen2.5:3b"
  }'
```

### Python Example

```python
import requests
import base64

response = requests.post(
    "http://localhost:8077/api/generate",
    json={
        "job_description": "Your job description here",
        "resume": "Your resume text here",
        "theme": "ink",
        "tailor": True
    }
)

data = response.json()

# Decode and save PDF
pdf_bytes = base64.b64decode(data["generated_content"])
with open("resume.pdf", "wb") as f:
    f.write(pdf_bytes)

print(f"Saved: {data['pdf_filename']}")
```

---

## Next Steps

- 🏗️ Check [architecture-diagram.md](architecture-diagram.md) for system architecture


---

**Happy Resume Building!** 🚀

