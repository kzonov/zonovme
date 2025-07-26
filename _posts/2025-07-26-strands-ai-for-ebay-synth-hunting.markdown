---
layout: post
title: "My Dive into Agentic AI: Building a Smart Shopping Assistant with Strands"
date: 2025-07-26 17:00:00.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- AI & Automation
- DevOps
tags:
- AI agents
- Strands
- AWS Bedrock
- automation
- web scraping
- Python
- Docker
- Terraform
meta:
  _edit_last: '1'
  hefo_before: '0'
  hefo_after: '0'
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/strands-ai-for-synth-hunting/"
excerpt: "Building an AI agent using Strands AI framework to automatically hunt for Teenage Engineering OP-1 synthesizers on eBay Kleinanzeigen. A DevOps engineer's journey into agentic AI for smart automation."
---

![Teenage Engineering OP-1 in a good condition](/assets/2025/07/op-1-in-good-condition.png)

As a DevOps engineer, I'm always looking for ways to automate repetitive tasks. Recently, I stumbled upon a perfect use case: hunting for a **Teenage Engineering OP-1** synthesizer on eBay Kleinanzeigen. These funny music devices rarely drop below €500, and finding one in good condition near Berlin requires constantly checking listings. Instead of manually refreshing the page every few hours, I decided to build my first AI agent using [Strands](https://github.com/strands-agents/sdk-python).



## The Problem: Manual Marketplace Monitoring

Picture this scenario: You want to buy a specific item (OP-1 synthesizer) but only if:
- Price is under €500
- Condition is "good" or better  
- No issues mentioned in the description
- Located within reasonable distance from Berlin
- Actually available (not already sold)

The traditional approach involves:
1. Bookmark eBay Kleinanzeigen search URL
2. Check it multiple times daily
3. Manually read each listing description
4. Evaluate condition and location
5. Miss good deals due to timing
6. Get frustrated with false positives

This is exactly the kind of tedious, rule-based task that AI agents excel at.

## What is Strands?

[Strands](https://strands-agents.github.io/sdk-python/) is a Python framework for building AI agents that can use tools and make decisions autonomously. Unlike simple chatbots, Strands agents can:

- **Browse websites** and extract information
- **Use custom tools** that you define (APIs, databases, notifications)
- **Make decisions** based on complex criteria
- **Execute multi-step workflows** without human intervention
- **Remember context** across tool calls

Built with the integration with AWS Bedrock in mind, Strands handles the complex orchestration between AI models and tools, letting you focus on defining what your agent should do rather than how to implement the AI plumbing.

## Traditional Approach vs AI Agent

### Traditional Automation (Brittle)
```python
import requests
from bs4 import BeautifulSoup
import re

# Hardcoded scraping - breaks when HTML changes
def check_ebay_kleinanzeigen():
    response = requests.get("https://www.kleinanzeigen.de/s-musik/berlin/op-1/k0c74l3331")
    soup = BeautifulSoup(response.content, 'html.parser')
    
    # Brittle CSS selectors
    listings = soup.find_all('div', class_='aditem-main')
    
    for listing in listings:
        price_elem = listing.find('strong', class_='aditem-main--middle--price-shipping--price')
        # More brittle parsing...
        if price_elem and '€' in price_elem.text:
            price = int(re.search(r'(\d+)', price_elem.text).group(1))
            if price < 500:
                # Still need to manually check description quality...
                return listing
```

This approach requires:
- Reverse engineering HTML structure
- Writing fragile CSS selectors
- Complex text parsing logic
- Maintenance when site changes
- No natural language understanding, thus no way to determine the condition of the listing

### AI Agent Approach
```python
from strands import Agent
from strands.models.bedrock import BedrockModel

# Natural language instructions
agent = Agent(
    model=BedrockModel(model_id="eu.anthropic.claude-3-7-sonnet-20250219-v1:0"),
    system_prompt="""
    Find Teenage Engineering OP-1 synthesizers on eBay Kleinanzeigen:
    - Price under €500
    - Good condition, no issues mentioned
    - Located within 50km of Berlin
    - Send notification if found
    """,
    tools=[browse_website, send_notification]
)

# Agent figures out how to search, evaluate, and notify
result = agent("Check for OP-1 deals on eBay Kleinanzeigen")
```

The AI agent:
- Understands natural language criteria
- Adapts to website changes
- Evaluates listing quality contextually
- Handles edge cases intelligently

And surprisingly this is roughly it, we could stop here. Above are the sufficient instructions to make your agent work. However, to make this blog post more actionable, let's dive a little bit deeper.

## Building the Agent Step by Step

Let's build this step by step, starting simple and adding complexity.

### Step 1: Basic Web Browsing Tool

First, we need a tool that can browse websites and extract content:

```python
from strands.tools import tool
import requests
from bs4 import BeautifulSoup

@tool
def browse_website(url: str) -> str:
    """Browse a website and return its text content.
    
    Args:
        url: The URL to browse
        
    Returns:
        The cleaned text content of the website
    """
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
    }
    
    response = requests.get(url, headers=headers, timeout=10)
    response.raise_for_status()
    
    soup = BeautifulSoup(response.content, 'html.parser')
    
    # Remove script and style elements
    for script in soup(["script", "style"]):
        script.decompose()
    
    # Get clean text
    text = soup.get_text()
    lines = (line.strip() for line in text.splitlines())
    chunks = (phrase.strip() for line in lines for phrase in line.split("  "))
    text = ' '.join(chunk for chunk in chunks if chunk)
    
    # Limit length to avoid overwhelming the model
    return text[:8000] if len(text) > 8000 else text
```

### Step 2: Notification Tool

Next, a simple notification tool (we'll use Telegram, but you could use email, Slack, etc.):

```python
@tool
def send_notification(message: str) -> str:
    """Send a notification message.
    
    Args:
        message: The message to send
        
    Returns:
        Success confirmation
    """
    import json
    import boto3
    
    # Get Telegram credentials from AWS Secrets Manager
    secrets_client = boto3.client('secretsmanager')
    secret = secrets_client.get_secret_value(SecretId='op1-finder-secrets')
    credentials = json.loads(secret['SecretString'])
    
    # Send via Telegram (simplified)
    telegram_url = f"https://api.telegram.org/bot{credentials['TELEGRAM_TOKEN']}/sendMessage"
    payload = {
        'chat_id': credentials['TELEGRAM_CHAT_ID'],
        'text': message,
        'parse_mode': 'Markdown'
    }
    
    response = requests.post(telegram_url, json=payload)
    response.raise_for_status()
    
    return "Notification sent successfully"
```

### Step 3: Agent Configuration

Now we create the agent with clear instructions:

```python
from strands import Agent
from strands.models.bedrock import BedrockModel

# Create the AI model
model = BedrockModel(
    model_id="eu.anthropic.claude-3-7-sonnet-20250219-v1:0",
    region="eu-west-1"
)

# System prompt with clear instructions
SYSTEM_PROMPT = """
You are an OP-1 synthesizer hunting agent for eBay Kleinanzeigen.

TASK: Find Teenage Engineering OP-1 synthesizers that meet these criteria:
- Price under €500
- Condition described as "good", "very good", "excellent", or "like new"
- NO issues, defects, or problems mentioned in description
- Located within 50km of Berlin (PLZ 10000-14999 or nearby cities like Potsdam, Brandenburg)
- Currently available (not marked as sold/reserved)

PROCESS:
1. Browse https://www.ebay-kleinanzeigen.de/s-musik/berlin/teenage+engineering+op-1/k0c74l3331
2. For each listing that seems promising:
   - Check the individual listing page for full details
   - Evaluate against all criteria carefully
   - If it matches, send notification with:
     * Title and brief description
     * Price and condition
     * Location
     * Direct link to listing
     * Reason why it matches criteria

Be thorough but only notify for genuine matches to avoid spam. Check not more than 5 listings.

IMPORTANT: Be verbose about your process. Log what you're doing:
- "Browsing search results page..."
- "Found X listings, checking each one..."
- "Checking listing: {title}..."
- "This listing matches/doesn't match because..."

TOOLS AVAILABLE:
- browse_website (web browsing)
- send_notification (alert when good deal found)
"""

# Create the agent
agent = Agent(
    model=model,
    system_prompt=SYSTEM_PROMPT,
    tools=[browse_website, send_notification]
)
```

### Step 4: Lambda Handler

Finally, wrap it in a Lambda function:

```python
import json
import logging
import boto3
from strands import Agent
from strands.models.bedrock import BedrockModel

# Set up logging
logging.basicConfig(level=logging.INFO)
log = logging.getLogger(__name__)

# ... (tool definitions from above) ...

def lambda_handler(event, context):
    """AWS Lambda handler for OP-1 finder agent."""
    
    log.info("🎹 Starting OP-1 finder agent...")
    
    try:
        # Create agent (same as Step 3)
        model = BedrockModel(model_id="eu.anthropic.claude-3-7-sonnet-20250219-v1:0")
        agent = Agent(
            model=model,
            system_prompt=SYSTEM_PROMPT,
            tools=[browse_website, send_notification]
        )
        
        # Run the agent
        result = agent("Check eBay Kleinanzeigen for OP-1 synthesizers matching my criteria")
        
        log.info(f"✅ Agent completed: {result}")
        
        return {
            'statusCode': 200,
            'body': json.dumps({
                'message': 'OP-1 search completed successfully',
                'result': str(result)
            })
        }
        
    except Exception as e:
        log.error(f"❌ Error running agent: {str(e)}")
        return {
            'statusCode': 500,
            'body': json.dumps({'error': str(e)})
        }
```

## Infrastructure with Terraform

Here's the AWS infrastructure to support our agent:

```hcl
# terraform/op1-finder.tf

########################################
# Secrets Manager for API keys
########################################
resource "aws_secretsmanager_secret" "op1_finder_secrets" {
  name = "op1-finder-secrets"
  
  tags = {
    Project = "op1-finder"
  }
}

resource "aws_secretsmanager_secret_version" "op1_finder_secrets_v1" {
  secret_id     = aws_secretsmanager_secret.op1_finder_secrets.id
  secret_string = jsonencode({
    TELEGRAM_TOKEN   = "your_telegram_bot_token"
    TELEGRAM_CHAT_ID = "your_telegram_chat_id"
  })
}

########################################
# IAM Role for Lambda
########################################
data "aws_iam_policy_document" "lambda_assume_role" {
  statement {
    actions = ["sts:AssumeRole"]
    principals {
      type        = "Service"
      identifiers = ["lambda.amazonaws.com"]
    }
  }
}

resource "aws_iam_role" "op1_finder_lambda_role" {
  name               = "op1-finder-lambda-role"
  assume_role_policy = data.aws_iam_policy_document.lambda_assume_role.json
}

# Basic Lambda execution
resource "aws_iam_role_policy_attachment" "lambda_basic_execution" {
  role       = aws_iam_role.op1_finder_lambda_role.name
  policy_arn = "arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole"
}

# Secrets Manager access
resource "aws_iam_role_policy" "lambda_secrets_access" {
  name = "lambda-secrets-access"
  role = aws_iam_role.op1_finder_lambda_role.id
  
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect   = "Allow"
        Action   = ["secretsmanager:GetSecretValue"]
        Resource = [aws_secretsmanager_secret.op1_finder_secrets.arn]
      }
    ]
  })
}

# Bedrock access for AI model. The access might be too broad, better to be refined
resource "aws_iam_role_policy" "lambda_bedrock_access" {
  name = "lambda-bedrock-access"
  role = aws_iam_role.op1_finder_lambda_role.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect   = "Allow"
        Action   = ["bedrock:*"]
        Resource = "*"
      }
    ]
  })
}

########################################
# ECR Repository for Lambda Image
########################################
resource "aws_ecr_repository" "op1_finder_image" {
  name = "op1-finder"
}

########################################
# Lambda Function
########################################
resource "aws_lambda_function" "op1_finder" {
  function_name = "op1-finder"
  role          = aws_iam_role.op1_finder_lambda_role.arn
  architectures = ["arm64"]

  package_type = "Image"
  image_uri    = "${aws_ecr_repository.op1_finder_image.repository_url}:latest"

  timeout     = 300  # 5 minutes
  memory_size = 512

  environment {
    variables = {
      SECRETS_ARN = aws_secretsmanager_secret.op1_finder_secrets.arn
    }
  }
}

########################################
# EventBridge for Scheduled Execution
########################################
resource "aws_cloudwatch_event_rule" "op1_finder_schedule" {
  name                = "op1-finder-schedule"
  description         = "Trigger OP-1 finder every 2 hours"
  schedule_expression = "rate(2 hours)"
}

resource "aws_cloudwatch_event_target" "op1_finder_target" {
  rule      = aws_cloudwatch_event_rule.op1_finder_schedule.name
  target_id = "op1-finder-target"
  arn       = aws_lambda_function.op1_finder.arn
}

resource "aws_lambda_permission" "allow_eventbridge" {
  action        = "lambda:InvokeFunction"
  function_name = aws_lambda_function.op1_finder.function_name
  principal     = "events.amazonaws.com"
  source_arn    = aws_cloudwatch_event_rule.op1_finder_schedule.arn
}

########################################
# Outputs
########################################
output "op1_finder_lambda_arn" {
  description = "ARN of the OP-1 finder Lambda function"
  value       = aws_lambda_function.op1_finder.arn
}

output "ecr_repository_url" {
  description = "ECR repository URL for Lambda images"
  value       = aws_ecr_repository.op1_finder_image.repository_url
}
```

### Dockerfile for Lambda

```dockerfile
FROM public.ecr.aws/lambda/python:3.12

# Copy function code
COPY handler.py ./

# Copy requirements and install
COPY requirements.txt .
RUN pip install --upgrade --no-cache-dir -r requirements.txt

# Set the Lambda handler
CMD ["handler.lambda_handler"]
```

### Requirements

```txt
strands-agents>=1.1.0
requests>=2.31.0
beautifulsoup4>=4.12.0
boto3>=1.34.0 
```

## The complete code

The complete working code example can be found in my public repository: [https://github.com/kzonov/ebay-kleinanzaigen-synth-finder](https://github.com/kzonov/ebay-kleinanzaigen-synth-finder)

## Result

Below is the real result I received from my Telegram bot. Getting on my bike, wait for me, my OP-1!

![Screenshot from a telegram bot](/assets/2025/07/telegram-result.png)

## Notes

I didn't go deep explaining the Terraform and Docker parts, as it isn't the idea of this post and I assume you're already familiar with these tools.

If you're using AWS Bedrock for the first time, it may require you to manually enable the model you're going to use. You can do it in the AWS Management Console by navigating go the Bedrock section.

## Conclusion

Agentic AI is a real paradigm shift from clearly defined but rather rigid automation to intelligent, adaptive systems. With frameworks like Strands, building AI agents has become surprisingly accessible - even for someone primarily focused on infrastructure like myself.

The OP-1 finder agent runs reliably, adapts to website changes, and has already helped me spot several good deals 😎 More importantly, it demonstrates how AI agents can handle complex, subjective tasks that would be difficult or impossible with traditional automation.

Whether you're hunting for synthesizers, monitoring job boards, or tracking inventory levels, AI agents offer a powerful new approach to automation that's both more intelligent and more maintainable than traditional methods.

## Resources

- [Strands Documentation](https://strands-agents.github.io/sdk-python/)
- [AWS Bedrock Models](https://docs.aws.amazon.com/bedrock/latest/userguide/model-ids.html)
- [AWS Lambda Container Images](https://docs.aws.amazon.com/lambda/latest/dg/images-create.html)

*Happy hunting! 🎹* 
