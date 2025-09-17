# Deploy Khoj to Render

## One-Click Deploy

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/officialprakashkumarsingh/khoj)

**Note**: The one-click deploy uses Render's free tier, which has some limitations:
- Apps sleep after 15 minutes of inactivity
- Limited to 750 hours per month
- Slower cold starts
- No persistent storage between deployments

For production use, consider upgrading to a paid plan after initial setup.

## Manual Deployment Steps

If you prefer to deploy manually or need to customize the deployment:

### 1. Fork the Repository
Fork this repository to your GitHub account.

### 2. Create a New Web Service on Render
1. Go to [Render Dashboard](https://dashboard.render.com/)
2. Click "New +" and select "Web Service"
3. Connect your GitHub account and select your forked repository
4. Use the following settings:

### 3. Service Configuration

**Basic Settings:**
- **Name**: khoj-app (or your preferred name)
- **Environment**: Python 3
- **Region**: Choose your preferred region
- **Branch**: main (or your default branch)

**Build & Deploy:**
- **Build Command**: 
  ```bash
  pip install --upgrade pip
  pip install -r requirements.txt
  cd src/interface/web && npm install && npm run build
  cd ../../../ && python src/khoj/manage.py collectstatic --noinput
  ```
- **Start Command**: 
  ```bash
  gunicorn -c gunicorn-config.py src.khoj.main:app
  ```

### 4. Create PostgreSQL Database
1. In your Render dashboard, click "New +" and select "PostgreSQL"
2. Configure the database:
   - **Name**: khoj-database
   - **Database**: khoj
   - **User**: khoj_user
   - **Region**: Same as your web service
   - **Plan**: Free (1 GB storage, limited connections)
3. After creating the database, note down the connection details

**Note**: The free PostgreSQL tier has limitations:
- 1 GB storage
- Limited connections
- Data may not persist between deployments
- Consider upgrading to a paid plan for production use

### 5. Environment Variables
Add these environment variables in your web service settings:

**Required Variables:**
```
POSTGRES_HOST=<from database service>
POSTGRES_PORT=<from database service>
POSTGRES_USER=<from database service>
POSTGRES_PASSWORD=<from database service>
POSTGRES_DB=<from database service>
KHOJ_DJANGO_SECRET_KEY=<generate a secure secret key>
KHOJ_DEBUG=False
KHOJ_ADMIN_EMAIL=admin@example.com
KHOJ_ADMIN_PASSWORD=<generate a secure password>
KHOJ_NO_HTTPS=False
PORT=10000
PYTHON_VERSION=3.12.7
NODE_VERSION=18
```

**Note**: The one-click deploy button will create the web service, but you'll need to manually create the PostgreSQL database and add the database environment variables to your web service.

**Optional Variables (for enhanced features):**
```
# AI Model Configuration
OPENAI_API_KEY=your_openai_api_key
ANTHROPIC_API_KEY=your_anthropic_api_key
GEMINI_API_KEY=your_gemini_api_key

# Web Search (optional)
SERPER_DEV_API_KEY=your_serper_api_key
JINA_API_KEY=your_jina_api_key

# Email (optional)
RESEND_API_KEY=your_resend_api_key

# Phone (optional)
TWILIO_ACCOUNT_SID=your_twilio_sid
TWILIO_AUTH_TOKEN=your_twilio_token
```

### 6. Deploy
Click "Create Web Service" to start the deployment process.

## Post-Deployment Setup

1. **Wait for Build**: The initial build may take 10-15 minutes
2. **Database Migration**: The app will automatically run database migrations
3. **Access Your App**: Once deployed, you'll get a URL like `https://khoj-app.onrender.com`
4. **Admin Access**: Use the admin credentials you set in environment variables

## Troubleshooting

### Common Issues:

1. **Build Timeout**: If the build times out, try using a higher-tier plan
2. **Memory Issues**: The free tier has limited memory; consider upgrading for production use
3. **Database Connection**: Ensure all PostgreSQL environment variables are correctly set

### Logs:
Check the service logs in your Render dashboard for detailed error messages.

## Customization

You can customize the deployment by:
- Modifying `render.yaml` for infrastructure as code
- Adjusting `gunicorn-config.py` for server configuration
- Adding additional environment variables for specific features

## Support

- [Render Documentation](https://render.com/docs)
- [Khoj Documentation](https://docs.khoj.dev)
- [Khoj Discord](https://discord.gg/BDgyabRM6e)