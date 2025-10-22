# Swiggy Food Order Emails to Google Sheet - N8N Workflow
Automated workflow to parse Swiggy food delivery order emails and track expenses in Google Sheets using n8n workflow automation with AI-powered email parsing.

## The Workflow Difference

| Self-Hosted with Ollama | Cloud-Based with OpenAI/Anthropic |
|-------------------------|-----------------------------------|
| ![Ollama Workflow](https://github.com/asispan/Food-Order-to-Google-Sheet-for-Financial-Planning/blob/main/food-order-tracking-on-google-sheet.png) | ![OpenAI Workflow](https://github.com/asispan/Food-Order-to-Google-Sheet-for-Financial-Planning/blob/main/openai-food-order-tracking-on-google-sheet.png) |
| Privacy-focused local processing | Cloud-powered enhanced accuracy |


## Overview

This repository contains n8n workflows that automatically extract order details from Swiggy email notifications and append them to a Google Sheet for personal finance tracking. The workflow leverages Large Language Models (LLMs) to intelligently parse unstructured email data into structured financial records.

## Features

- Automated email monitoring for Swiggy order confirmations
- AI-powered email parsing using LLM models (Ollama, OpenAI, or Anthropic)
- Structured data extraction with consistent JSON output
- Automatic Google Sheets integration for expense tracking
- Scheduled execution with customizable intervals
- Support for multiple Swiggy order types (Food Delivery, Dineout, Instamart)

## Prerequisites

Before setting up this workflow, ensure you have the following:

- n8n instance (self-hosted or cloud)
- Google account with Google Sheets API access
- Gmail account with Swiggy order emails
- One of the following LLM providers:
  - Ollama (self-hosted, free)
  - OpenAI API key
  - Anthropic API key

## Architecture

The workflow consists of the following components:

1. **Schedule Trigger**: Runs automatically at scheduled intervals (default: daily at 1:30 AM)
2. **Manual Trigger**: Allows on-demand execution for testing
3. **Gmail Integration**: Fetches recent Swiggy order emails
4. **AI Email Parser**: Extracts order details using LLM
5. **Clean JSON Node**: Normalizes AI output into consistent format
6. **Google Sheets Append**: Writes parsed data to tracking spreadsheet

## Installation and Setup

### Step 1: Install n8n

If you do not already have n8n installed, choose one of the following methods:

**Option A: n8n Cloud (Recommended for beginners)**
1. Visit https://n8n.io and sign up for a cloud account
2. Access your n8n instance through the web interface

**Option B: Self-Hosted with Docker**
```bash
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  docker.n8n.io/n8nio/n8n
```

**Option C: Self-Hosted with npm**
```bash
npm install n8n -g
n8n start
```

Access n8n at `http://localhost:5678`

### Step 2: Configure Google Sheets API Credentials

1. **Create Google Cloud Project**
   - Navigate to [Google Cloud Console](https://console.cloud.google.com)
   - Click "Create Project" and provide a project name
   - Select the newly created project

2. **Enable Google Sheets API**
   - In the Cloud Console sidebar, go to "APIs & Services" > "Library"
   - Search for "Google Sheets API"
   - Click "Enable"

3. **Configure OAuth Consent Screen**
   - Navigate to "APIs & Services" > "OAuth consent screen"
   - Select "External" as user type (unless using Google Workspace)
   - Fill in required fields:
     - App name
     - User support email
     - Developer contact email
   - Click "Save and Continue"

4. **Create OAuth 2.0 Credentials**
   - Go to "APIs & Services" > "Credentials"
   - Click "Create Credentials" > "OAuth client ID"
   - Select "Web application" as application type
   - Add a name for your OAuth client
   - In n8n, navigate to Credentials and start creating a "Google Sheets OAuth2 API" credential
   - Copy the "OAuth Redirect URL" from n8n
   - Paste this URL into "Authorized redirect URIs" in Google Cloud Console
   - Click "Create"
   - Copy the Client ID and Client Secret
   - Paste these values into your n8n credential configuration
   - Complete the OAuth flow by clicking "Connect" in n8n

### Step 3: Configure Gmail API Credentials

Follow the same process as Google Sheets, but enable "Gmail API" instead:
1. In Google Cloud Console, enable "Gmail API" from the Library
2. Create OAuth credentials using the same project
3. In n8n, create "Gmail OAuth2" credentials
4. Copy the OAuth Redirect URL and add it to authorized redirect URIs
5. Complete the authentication flow

### Step 4: Set Up LLM Provider

Choose one of the following options based on your preference:

**Option A: Ollama (Recommended for Self-Hosted, Privacy-Focused Setup)**

1. **Install Ollama**
   ```bash
   # Linux
   curl -fsSL https://ollama.com/install.sh | sh
   
   # macOS
   brew install ollama
   
   # Windows
   # Download installer from https://ollama.com
   ```

2. **Pull a Compatible Model**
   ```bash
   ollama pull gpt-oss:latest
   # or use other models like llama2, mistral, etc.
   ollama list  # Verify installation
   ```

3. **Start Ollama Server**
   ```bash
   ollama serve
   # Server runs on http://localhost:11434 by default
   ```

4. **Configure Ollama Credentials in n8n**
   - In n8n, go to Credentials > Add Credential
   - Search for "Ollama API"
   - Base URL: `http://localhost:11434` (or your Ollama server URL)
   - Save the credential

**Option B: OpenAI**

1. Obtain API key from [OpenAI Platform](https://platform.openai.com)
2. In n8n Credentials, create "OpenAI API" credential
3. Enter your API key
4. Save the credential

**Option C: Anthropic**

1. Obtain API key from [Anthropic Console](https://console.anthropic.com)
2. In n8n Credentials, create "Anthropic API" credential
3. Enter your API key
4. Save the credential

### Step 5: Create Google Sheet

1. Create a new Google Sheet for tracking orders
2. Set up the following column headers in the first row:
   - Restaurant
   - Date & Time
   - Type
   - Order Id
   - Item Total
   - Discount
   - Packaging Charges
   - Platform Fee
   - Delivery Fee
   - Taxes
   - Order Total Final

3. Copy the Google Sheet URL for workflow configuration

### Step 6: Import Workflow

1. **Download Workflow File**
   - Download either `Swiggy-Order-Email-to-Google-Sheets-Self-Hosted.json` (for Ollama) or `OpenAI-Swiggy-Order-Email-to-Google-Sheets.json` (for OpenAI/Anthropic) from this repository

2. **Import into n8n**
   - In n8n, click the three dots menu (top right) > "Import from File"
   - Select the downloaded JSON file
   - The workflow will appear on your canvas

3. **Configure Credentials**
   - Open each node that requires credentials (marked with warning icons)
   - Select or create the appropriate credentials:
     - Gmail OAuth2 (for Gmail nodes)
     - Google Sheets OAuth2 (for Append row node)
     - Ollama API / OpenAI API / Anthropic API (for LLM nodes)

4. **Update Google Sheet Reference**
   - Open the "Append row in sheet" node
   - Update the "Document ID" to your Google Sheet URL
   - Select the appropriate sheet name (usually "Sheet1")

### Step 7: Configure Email Filter

Modify the Gmail filter query if needed:
- Open the "Gmail - Get MessageIDs" node
- Default filter: `from:(noreply@swiggy.in) (subject:order OR subject:Instamart OR subject:gourmet)`
- Adjust the query to match your email requirements
- Modify the "limit" parameter to control how many recent emails to process

### Step 8: Test the Workflow

1. **Manual Test**
   - Ensure the workflow is saved
   - Click "Execute Workflow" button (or use Manual Trigger node)
   - Verify that emails are fetched and data appears in Google Sheet
   - Check for any errors in node execution

2. **Activate Scheduled Execution**
   - Toggle the workflow to "Active" in the top right
   - The Schedule Trigger will run automatically at 1:30 AM daily
   - Monitor executions in the "Executions" panel

## Workflow Configuration

### Primary Workflow: Self-Hosted with Ollama

This workflow (`Swiggy-Order-Email-to-Google-Sheets-Self-Hosted.json`) uses a self-hosted Ollama LLM model for email parsing.

**Key Nodes:**
- **Schedule Trigger**: Executes daily at 1:30 AM (customizable)
- **Manual Trigger**: For testing and manual execution
- **Gmail - Get MessageIDs**: Fetches last 3 Swiggy order emails
- **Get Whole Email**: Retrieves full email content including HTML body
- **AI Email Parser**: Uses LangChain agent to extract structured data
- **Ollama Chat Model**: Local LLM for parsing (model: `gpt-oss:latest`)
- **Structured Output Parser**: Ensures consistent JSON format
- **Clean JSON**: JavaScript code to handle parsing edge cases
- **Append row in sheet**: Writes data to Google Sheets

**Data Extraction Fields:**
- Restaurant Name
- Order Date and Time
- Order Type (Food, Dineout, or Instamart)
- Order ID
- Item Total
- Discount
- Packaging Charges
- Platform Fee
- Delivery Fee
- Taxes
- Order Total Final

### Alternative Workflow: OpenAI/Anthropic

The `OpenAI-Swiggy-Order-Email-to-Google-Sheets.json` workflow provides the same functionality but uses cloud-based LLM providers.

**Configuration Options:**
- Uses OpenAI Chat Model (gpt-3.5-turbo) as primary LLM
- Anthropic Chat Model (Claude Haiku 4.5) available as alternative
- Structured Output Parser connected to ensure consistent JSON output

**To Switch LLM Models:**
1. Open the "AI Email Parser" node
2. Disconnect the current LLM connection
3. Connect your preferred model node:
   - OpenAI Chat Model
   - Anthropic Chat Model
   - Ollama Chat Model (if switching to self-hosted)

### Structured Output Parser Configuration

The Structured Output Parser ensures that the LLM returns data in a consistent format. It uses a JSON schema example:

```json
[
  {
    "restaurantName": "MTR - Lalbagh Road",
    "date": "Saturday, October 11, 2025 12:15 PM",
    "type": "Food",
    "orderId": "219136361600127",
    "orderTotal": "420.00",
    "discount": "20.00",
    "packagingCharges": "15.00",
    "platformFee": "9.99",
    "deliveryFee": "0.00",
    "taxes": "21.00",
    "orderTotalFinal": "445.99"
  }
]
```

This schema guides the LLM to extract and format the data correctly from unstructured email content.

### Clean JSON Node

The Clean JSON node contains JavaScript code that handles various edge cases in AI-generated output:

**Functionality:**
- Parses potentially malformed JSON from LLM responses
- Handles escaped characters and embedded quotes
- Extracts JSON arrays from mixed text responses
- Flattens nested arrays
- Filters out empty or invalid entries
- Formats data for Google Sheets compatibility

This node is critical for ensuring reliable data flow even when the LLM output is not perfectly formatted.

## Security Best Practices

### Protecting Sensitive Information

**Never Share Credentials:**
- The exported JSON files contain credential IDs but not the actual secrets
- Before sharing workflows, verify that no hardcoded API keys exist
- Use n8n's built-in credential management system exclusively

**Environment Variables (Self-Hosted Only):**

For enhanced security in self-hosted deployments, use environment variables:

```bash
# Set environment variables for n8n
export N8N_BASIC_AUTH_ACTIVE=true
export N8N_BASIC_AUTH_USER=your_username
export N8N_BASIC_AUTH_PASSWORD=your_secure_password
export N8N_SECURE_COOKIE=true
export N8N_BLOCK_ENV_ACCESS_IN_NODE=true
```

**Google Sheet Security:**
- Limit sharing permissions on your tracking spreadsheet
- Only grant access to necessary Google accounts
- Consider using a dedicated service account for API access

**Credential Rotation:**
- Periodically regenerate API keys and OAuth tokens
- Update credentials in n8n credential manager
- Revoke unused or old credentials from provider dashboards

**Webhook Security:**
- Use unique, non-guessable webhook URLs
- Implement IP whitelisting if possible
- Enable HTTPS for all webhook endpoints

## Customization

### Modify Schedule Trigger

To change when the workflow runs:
1. Open the "Schedule Trigger" node
2. Select "Custom (Cron)" as trigger interval
3. Enter a cron expression:
   - Every hour: `0 * * * *`
   - Every 6 hours: `0 */6 * * *`
   - Daily at 9 AM: `0 9 * * *`
   - Weekly on Monday at 8 AM: `0 8 * * 1`

Use [Crontab Guru](https://crontab.guru/) to generate custom cron expressions.

### Adjust Email Query

Modify the Gmail filter in "Gmail - Get MessageIDs" node:
```
# Fetch all Swiggy emails
from:(noreply@swiggy.in)

# Fetch only food delivery orders
from:(noreply@swiggy.in) subject:order

# Fetch from specific date
from:(noreply@swiggy.in) after:2025/01/01

# Fetch unread emails only
from:(noreply@swiggy.in) is:unread
```

### Modify Extracted Fields

To change which data fields are extracted:
1. Open the "AI Email Parser" node
2. Update the prompt text to include new fields
3. Open the "Structured Output Parser" node
4. Update the JSON schema example with new field names
5. Open the "Append row in sheet" node
6. Add corresponding column mappings

### Support for Other Food Delivery Services

This workflow can be adapted for other food delivery platforms:
1. Update Gmail filter query to match the provider's email address
2. Modify AI Email Parser prompt to match email format
3. Adjust Structured Output Parser schema if field names differ
4. Test thoroughly with sample emails

## Troubleshooting

### Common Issues and Solutions

**Issue: Workflow Not Triggering Automatically**
- Verify workflow is set to "Active"
- Check Schedule Trigger configuration
- Review timezone settings in n8n settings
- For self-hosted: ensure n8n process is running continuously

**Issue: Gmail Authentication Failed**
- Re-authenticate Gmail OAuth2 credentials
- Verify Gmail API is enabled in Google Cloud Console
- Check OAuth redirect URL matches n8n configuration
- Ensure scopes include "gmail.readonly"

**Issue: Google Sheets Write Failed**
- Confirm Google Sheets API is enabled
- Verify OAuth2 credentials are valid
- Check that sheet ID and sheet name are correct
- Ensure column mappings match sheet headers
- Confirm Google account has edit permissions

**Issue: LLM Not Parsing Data Correctly**
- Verify LLM service is accessible (Ollama server running, API keys valid)
- Review AI Email Parser prompt for clarity
- Check Structured Output Parser schema matches expected format
- Examine raw email content for format changes
- Test with different LLM models (OpenAI may be more reliable than smaller local models)

**Issue: Clean JSON Node Failing**
- Review the JavaScript code for syntax errors
- Check execution logs for specific error messages
- Verify AI output contains valid JSON structure
- Test with manual data input to isolate parsing issues

**Issue: Duplicate Entries in Google Sheet**
- Implement deduplication logic using Order ID
- Consider adding a filter node to check for existing entries
- Adjust Gmail query to fetch only unread or recent emails

**Issue: Ollama Connection Refused**
- Verify Ollama server is running: `curl http://localhost:11434`
- Check Ollama credential configuration in n8n
- Ensure firewall allows connections to Ollama port
- For Docker deployments: verify network configuration

### Performance Optimization

**Reduce API Costs:**
- Limit the number of emails fetched per execution
- Use smaller, more efficient LLM models
- Implement caching for repeated queries

**Improve Parsing Accuracy:**
- Provide more detailed examples in Structured Output Parser
- Use higher-quality LLM models (GPT-4, Claude Opus)
- Implement validation logic in Clean JSON node
- Test prompt variations for better extraction

**Speed Up Execution:**
- Use Ollama for fastest local processing
- Reduce unnecessary node operations
- Optimize JavaScript code in Clean JSON node

## Advanced Configuration

### Using Multiple LLM Providers

The workflow supports connecting multiple LLM nodes simultaneously:
1. Keep all three LLM model nodes in the workflow
2. Connect only one to the AI Email Parser at a time
3. Switch between models by reconnecting the desired node
4. Compare results to determine the most accurate model for your use case

### Implementing Error Handling

Add error handling to the workflow:
1. Enable "Continue on Fail" in critical nodes
2. Add an "If" node after AI Email Parser to check for valid output
3. Route failed items to an error notification node (Email, Slack, etc.)
4. Log errors to a separate Google Sheet for monitoring

### Timezone Configuration

For self-hosted instances, set timezone via environment variable:
```bash
export GENERIC_TIMEZONE="Asia/Kolkata"
export TZ="Asia/Kolkata"
```

For n8n Cloud:
- Navigate to Settings > General
- Select your timezone from the dropdown
- Save changes

## Contributing

Contributions are welcome to improve this workflow:
- Report issues or bugs via GitHub Issues
- Suggest enhancements or new features
- Submit pull requests with improvements
- Share variations for other food delivery services

## License

This project is open source and available for personal and commercial use.

## Acknowledgments

- n8n community for workflow automation platform
- Ollama project for local LLM hosting
- OpenAI and Anthropic for LLM APIs
- LangChain for agent framework

## Related Resources

- [n8n Documentation](https://docs.n8n.io)
- [Ollama Documentation](https://ollama.com)
- [Google Sheets API Documentation](https://developers.google.com/sheets/api)
- [Gmail API Documentation](https://developers.google.com/gmail/api)
- [LangChain Documentation](https://docs.langchain.com)

## Support

For questions or support:
- Review the [n8n community forum](https://community.n8n.io)
- Check the [n8n documentation](https://docs.n8n.io)
- Open an issue on this repository

## Version History

- v1.0 - Initial release with Ollama and OpenAI support
- Self-hosted and cloud deployment options
- Automated scheduling and manual triggers
- Support for Food, Dineout, and Instamart orders
