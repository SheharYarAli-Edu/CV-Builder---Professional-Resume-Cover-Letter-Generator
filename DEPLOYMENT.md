# 🚀 Vercel Deployment Guide

This guide will help you deploy the CV Builder application to Vercel with serverless Python backend and React frontend.

## 📋 Prerequisites

- A Vercel account (free tier works fine)
- A GitHub/GitLab/Bitbucket account
- DeepSeek API key (get it from https://platform.deepseek.com/)

## 🏗️ Project Structure

```
CV_builder-main/
├── api/                           # Serverless Python API routes
│   ├── _utils.py                  # Helper utilities
│   ├── health.py                  # Health check endpoint
│   ├── questionnaire.py           # Resume questionnaire
│   ├── generate-cv.py             # CV generation
│   ├── questionnaire-cover-letter.py
│   ├── generate-cover-letter.py
│   ├── ats-analyze.py             # ATS analysis
│   └── generate-resume-from-job.py
├── resume-builder/                # React frontend
│   ├── src/
│   ├── public/
│   └── package.json
├── Templates/                     # CV HTML templates
├── Cover_Letter/                  # Cover letter templates
├── vercel.json                    # Vercel configuration
├── requirements.txt               # Python dependencies
└── .vercelignore                  # Files to ignore during deployment
```

## 📦 Step-by-Step Deployment

### 1. Prepare Your Repository

1. Push your code to GitHub, GitLab, or Bitbucket
2. Ensure all the new files are committed:
   - `vercel.json`
   - `requirements.txt`
   - `.vercelignore`
   - `/api` directory with all serverless functions

```bash
git add .
git commit -m "Prepare for Vercel deployment"
git push origin main
```

### 2. Connect to Vercel

1. Go to [vercel.com](https://vercel.com) and sign in
2. Click **"Add New Project"**
3. Import your GitHub repository
4. Vercel will auto-detect the configuration from `vercel.json`

### 3. Configure Build Settings

Vercel should automatically detect:
- **Framework Preset**: Create React App (for resume-builder)
- **Build Command**: `cd resume-builder && npm install && npm run build`
- **Output Directory**: `resume-builder/build`
- **Install Command**: `npm install`

If not auto-detected, set these manually:

**Root Directory**: `./` (leave as root)

**Build & Development Settings**:
- Build Command: `cd resume-builder && npm run build`
- Output Directory: `resume-builder/build`
- Install Command: `cd resume-builder && npm install`

### 4. Set Environment Variables

In Vercel dashboard → Your Project → Settings → Environment Variables, add:

| Name | Value | Environment |
|------|-------|-------------|
| `DEEPSEEK_API_KEY` | `your_deepseek_api_key_here` | Production, Preview, Development |

**Important**: Get your DeepSeek API key from https://platform.deepseek.com/

### 5. Deploy

Click **"Deploy"** and wait for the build to complete (3-5 minutes).

Vercel will:
1. Install Python dependencies from `requirements.txt`
2. Build the React frontend
3. Deploy serverless Python functions to `/api/*`
4. Set up automatic HTTPS

### 6. Access Your App

Once deployed, you'll get a URL like:
```
https://your-project-name.vercel.app
```

Test the API:
```
https://your-project-name.vercel.app/api/health
```

## 🔧 Configuration Details

### React Frontend Environment

The frontend automatically detects the API URL. In production on Vercel, it uses relative paths `/api/*`.

If you need to override, set in Vercel environment variables:
```
REACT_APP_BACKEND_URL=https://your-project-name.vercel.app
```

### Python Runtime

Vercel uses Python 3.9+ runtime. Dependencies are specified in `requirements.txt`:
- `requests` - HTTP client for DeepSeek API
- `PyMuPDF` - PDF processing for ATS analysis
- `python-dotenv` - Environment variable management

### Serverless Function Limits

- **Timeout**: 60 seconds (configured in `vercel.json`)
- **Memory**: 1024 MB (default)
- **Regions**: Automatically distributed globally

If AI generation takes longer, consider:
- Upgrading to Vercel Pro (300s timeout)
- Optimizing prompts for faster responses
- Using streaming responses

## 🌐 Custom Domain (Optional)

1. Go to Project Settings → Domains
2. Add your custom domain
3. Follow DNS configuration instructions
4. Vercel handles SSL certificates automatically

## 🔄 Continuous Deployment

Every push to your main branch triggers automatic deployment:
```bash
git add .
git commit -m "Update features"
git push origin main
```

Vercel will:
- Build and deploy automatically
- Run preview deployments for pull requests
- Provide deployment logs and analytics

## 🐛 Troubleshooting

### Build Fails

**Error**: "Module not found"
- Check `requirements.txt` has all dependencies
- Verify `package.json` in `resume-builder/` is correct

**Error**: "Build timeout"
- Check build command is correct
- Ensure `resume-builder` directory exists

### API Returns 500 Error

1. Check Vercel Function Logs:
   - Dashboard → Your Project → Deployments → Click Latest → Functions
2. Verify environment variable `DEEPSEEK_API_KEY` is set
3. Check DeepSeek API key is valid and has credits

### CORS Errors

CORS is already configured in `api/_utils.py` with wildcard origin (`*`).

To restrict to your domain only:
```python
# In api/_utils.py, update cors_headers():
'Access-Control-Allow-Origin': 'https://your-domain.vercel.app',
```

### Function Timeout

If AI generation exceeds 60s:
1. Upgrade to Vercel Pro for 300s timeout
2. Or optimize DeepSeek prompts
3. Or implement client-side polling/retry logic

## 📊 Monitoring

**Function Logs**: Dashboard → Functions tab
**Analytics**: Dashboard → Analytics tab
**Speed Insights**: Dashboard → Speed tab

## 💰 Cost

**Vercel Free Tier**:
- ✅ Unlimited deployments
- ✅ 100 GB bandwidth/month
- ✅ Serverless functions (60s timeout)
- ✅ Automatic HTTPS

**DeepSeek API**:
- Pay per token usage
- Check pricing at https://platform.deepseek.com/

## 🔐 Security Best Practices

1. **Never commit** `.env` files with real API keys
2. Use Vercel environment variables for secrets
3. Rotate API keys periodically
4. Monitor API usage to detect abuse
5. Consider rate limiting for production

## 📚 Additional Resources

- [Vercel Documentation](https://vercel.com/docs)
- [Vercel Python Runtime](https://vercel.com/docs/functions/serverless-functions/runtimes/python)
- [DeepSeek API Docs](https://platform.deepseek.com/docs)

## 🆘 Support

If you encounter issues:
1. Check Vercel Function Logs
2. Review this deployment guide
3. Check DeepSeek API status
4. Create an issue on GitHub

---

**Happy Deploying! 🎉**
