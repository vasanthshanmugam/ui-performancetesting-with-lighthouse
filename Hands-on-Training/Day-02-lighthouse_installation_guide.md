# Episode 2: Installing Lighthouse - Step by Step Guide

## Method 1: CLI Installation 🖥️

### Prerequisites
- Node.js (version 16 or higher)
- npm package manager
- Google Chrome browser

### Step-by-Step Installation

1. **Check Node.js version**
   ```bash
   node --version
   ```
   *Should show v16.0.0 or higher*

2. **Verify npm is working**
   ```bash
   npm --version
   ```

3. **Test Lighthouse without global installation (Recommended)**
   ```bash
   npx lighthouse@10.4.0 https://google.com --only-categories=performance
   ```
   *This downloads and runs Lighthouse without installing it globally*

4. **If you prefer global installation (Optional)**
   ```bash
   npm install -g lighthouse
   ```
   
   **Note:** If using nvm (Node Version Manager), global installations can cause issues. Stick with npx.

### Recommended CLI Usage (Using npx)

```bash
# Basic performance audit
npx lighthouse@10.4.0 https://google.com --only-categories=performance

# View report in browser
npx lighthouse@10.4.0 https://google.com --only-categories=performance --view

# Multiple categories
npx lighthouse@10.4.0 https://google.com --only-categories=performance,accessibility,seo

# Mobile simulation
npx lighthouse@10.4.0 https://google.com --only-categories=performance --preset=perf --form-factor=mobile

# Generate JSON output
npx lighthouse@10.4.0 https://google.com --only-categories=performance --output=json --output-path=./report.json

# Desktop audit
npx lighthouse@10.4.0 https://google.com --only-categories=performance --form-factor=desktop
```

### Why npx is Better Than Global Installation

**✅ Advantages of npx:**
- No global installation conflicts
- Works with nvm (Node Version Manager)
- Always uses the specified version
- No permission issues
- Cleaner development environment

**❌ Global installation problems:**
- Path conflicts with nvm
- Version management issues
- Permission problems on some systems
- Chrome connection failures

---

## Method 2: Chrome DevTools (Built-in) 🌐

### Step-by-Step Process

1. **Open Chrome Browser**
   - Ensure you're using Google Chrome (not Edge, Firefox, or other browsers)

2. **Navigate to your website**
   ```
   https://google.com
   ```

3. **Open Developer Tools**
   - **Windows/Linux:** `F12` or `Ctrl + Shift + I`
   - **Mac:** `Cmd + Option + I`
   - **Right-click:** Select "Inspect"

4. **Find the Lighthouse tab**
   - Look for "Lighthouse" in the top tab bar
   - If not visible, click the `>>` arrow to see more tabs

5. **Configure your audit**
   - **Device:** Choose Mobile or Desktop
   - **Categories:** Select what to audit
     - ✅ Performance (recommended to start)
     - ✅ Accessibility  
     - ✅ Best Practices
     - ✅ SEO
     - ✅ Progressive Web App

6. **Run the audit**
   - Click "Analyze page load"
   - Wait for the audit to complete (30-60 seconds)

7. **View your results**
   - Review scores and recommendations
   - Expand sections for detailed insights

### DevTools Pro Tips
- **Clear cache:** Check "Clear storage" before running
- **Throttling:** DevTools simulates slow 3G by default
- **Multiple runs:** Run 3-5 times and average the scores
- **Incognito mode:** Use for consistent results without extensions

---

## Method 3: Node.js Integration 🔧

### Installation

1. **Initialize your project (if new)**
   ```bash
   mkdir lighthouse-project
   cd lighthouse-project
   npm init -y
   ```

2. **Install Lighthouse as a project dependency**
   ```bash
   npm install lighthouse@10.4.0
   ```

3. **Install Chrome Launcher (required)**
   ```bash
   npm install chrome-launcher
   ```

### Basic Node.js Implementation

4. **Create a test script** (`lighthouse-test.js`)
   ```javascript
   const lighthouse = require('lighthouse');
   const chromeLauncher = require('chrome-launcher');
   const fs = require('fs');

   async function runLighthouse() {
     console.log('Starting Lighthouse audit...');
     let chrome;
     
     try {
       // Launch Chrome
       chrome = await chromeLauncher.launch({
         chromeFlags: ['--headless', '--no-sandbox', '--disable-gpu']
       });

       // Run Lighthouse audit
       const options = {
         logLevel: 'info',
         output: 'html',
         onlyCategories: ['performance'],
         port: chrome.port
       };

       // Use lighthouse.default if lighthouse is not a function
       const lighthouseRunner = lighthouse.default || lighthouse;
       const runnerResult = await lighthouseRunner('https://google.com', options);

       // Save report
       fs.writeFileSync('lighthouse-report.html', runnerResult.report);
       console.log('Report saved to lighthouse-report.html');

       // Log scores
       const performanceScore = runnerResult.lhr.categories.performance.score * 100;
       console.log(`Performance Score: ${performanceScore}/100`);

       // Log key metrics
       const metrics = runnerResult.lhr.audits;
       console.log(`First Contentful Paint: ${Math.round(metrics['first-contentful-paint'].numericValue)}ms`);
       console.log(`Largest Contentful Paint: ${Math.round(metrics['largest-contentful-paint'].numericValue)}ms`);
       
     } catch (error) {
       console.error('Error running Lighthouse:', error.message);
     } finally {
       // Always close Chrome
       if (chrome) {
         await chrome.kill();
       }
     }
   }

   runLighthouse();

### Alternative Working Script (If above still fails)

If you're still getting module issues, try this ES6 import approach:

**Create `package.json` with module support:**
```json
{
  "name": "lighthouse-test",
  "version": "1.0.0",
  "type": "module",
  "dependencies": {
    "lighthouse": "^10.4.0",
    "chrome-launcher": "^0.15.2"
  }
}
```

**Create `lighthouse-test.mjs`:**
```javascript
import lighthouse from 'lighthouse';
import chromeLauncher from 'chrome-launcher';
import fs from 'fs';

async function runLighthouse() {
  console.log('Starting Lighthouse audit...');
  let chrome;
  
  try {
    chrome = await chromeLauncher.launch({
      chromeFlags: ['--headless', '--no-sandbox', '--disable-gpu']
    });

    const options = {
      logLevel: 'info',
      output: 'html',
      onlyCategories: ['performance'],
      port: chrome.port
    };

    const runnerResult = await lighthouse('https://google.com', options);
    
    fs.writeFileSync('lighthouse-report.html', runnerResult.report);
    console.log('✅ Report saved to lighthouse-report.html');
    
    const score = Math.round(runnerResult.lhr.categories.performance.score * 100);
    console.log(`🎯 Performance Score: ${score}/100`);
    
  } catch (error) {
    console.error('❌ Error:', error.message);
  } finally {
    if (chrome) await chrome.kill();
  }
}

runLighthouse();
```

**Run with:**
```bash
node lighthouse-test.mjs
```

### Advanced Node.js Example - Multiple Pages

**Create `multiple-pages-lh-test.js`:**
```javascript
const lighthouse = require('lighthouse');
const chromeLauncher = require('chrome-launcher');

async function auditMultiplePages(urls) {
  console.log('Starting multiple page audit...');
  let chrome;
  
  try {
    chrome = await chromeLauncher.launch({
      chromeFlags: ['--headless', '--no-sandbox', '--disable-gpu']
    });
    
    const results = [];
    
    // Handle both CommonJS and ES6 module exports
    const lighthouseRunner = lighthouse.default || lighthouse;
    
    for (const url of urls) {
      console.log(`🔍 Auditing: ${url}`);
      
      try {
        const runnerResult = await lighthouseRunner(url, {
          port: chrome.port,
          onlyCategories: ['performance'],
          settings: {
            maxWaitForFcp: 15 * 1000,
            maxWaitForLoad: 35 * 1000
          }
        });
        
        const lhr = runnerResult.lhr;
        
        results.push({
          url: url,
          performance: Math.round(lhr.categories.performance.score * 100),
          fcp: Math.round(lhr.audits['first-contentful-paint'].numericValue),
          lcp: Math.round(lhr.audits['largest-contentful-paint'].numericValue),
          cls: parseFloat(lhr.audits['cumulative-layout-shift'].numericValue.toFixed(3))
        });
        
        console.log(`✅ ${url} completed`);
        
      } catch (urlError) {
        console.log(`❌ Failed to audit ${url}:`, urlError.message);
        results.push({
          url: url,
          performance: 'Error',
          fcp: 'Error',
          lcp: 'Error',
          cls: 'Error'
        });
      }
    }
    
    return results;
    
  } catch (error) {
    console.error('Error in audit process:', error.message);
    return [];
  } finally {
    if (chrome) {
      await chrome.kill();
      console.log('Chrome closed');
    }
  }
}

// Usage
auditMultiplePages([
  'https://www.google.com',
  'https://github.com',
  'https://stackoverflow.com'
]).then(results => {
  console.log('\n=== 📊 Audit Results ===');
  console.table(results);
}).catch(error => {
  console.error('Script failed:', error.message);
});
```

**Alternative ES6 Module Version** (`multiple-pages-lh-test.mjs`):
```javascript
import lighthouse from 'lighthouse';
import chromeLauncher from 'chrome-launcher';

async function auditMultiplePages(urls) {
  console.log('Starting multiple page audit...');
  let chrome;
  
  try {
    chrome = await chromeLauncher.launch({
      chromeFlags: ['--headless', '--no-sandbox', '--disable-gpu']
    });
    
    const results = [];
    
    for (const url of urls) {
      console.log(`🔍 Auditing: ${url}`);
      
      const runnerResult = await lighthouse(url, {
        port: chrome.port,
        onlyCategories: ['performance']
      });
      
      results.push({
        url: url,
        performance: Math.round(runnerResult.lhr.categories.performance.score * 100),
        fcp: Math.round(runnerResult.lhr.audits['first-contentful-paint'].numericValue),
        lcp: Math.round(runnerResult.lhr.audits['largest-contentful-paint'].numericValue)
      });
    }
    
    return results;
    
  } finally {
    if (chrome) await chrome.kill();
  }
}

// Usage
const results = await auditMultiplePages([
  'https://www.google.com',
  'https://github.com',
  'https://stackoverflow.com'
]);

console.log('\n=== 📊 Audit Results ===');
console.table(results);
```

---

## Troubleshooting Common Issues 🔧

### CLI Issues - Chrome Connection Problems

**❌ Error: "Unable to connect to Chrome" or "ECONNREFUSED 127.0.0.1:4335"**

**✅ Solution: Use npx instead of global installation**
```bash
npx lighthouse@10.4.0 https://google.com --only-categories=performance
```

**If you're using nvm (Node Version Manager):**
```bash
# Check current Node version
nvm current

# Use npx (recommended)
npx lighthouse@10.4.0 https://google.com --only-categories=performance --view
```

**For persistent Chrome issues:**
```bash
# Kill existing Chrome processes (Windows)
taskkill /f /im chrome.exe

# Then run with Chrome flags
npx lighthouse@10.4.0 https://google.com --chrome-flags="--no-sandbox --disable-web-security" --only-categories=performance
```

### Other Common Issues

**Node.js version errors:**
- Update to Node.js 16+ 
- Use `nvm use 18` if you have nvm

**Permission errors:**
- Use npx instead of global install
- On Windows, run Command Prompt as Administrator

**Windows temp file cleanup errors:**
```
EPERM, Permission denied: \\?\C:\Users\...\lighthouse.xxxxx
```
- This happens after successful audits
- Results are still valid - check with `console.table(results)`
- To fix: Run as Administrator or ignore (doesn't affect functionality)

**Slow audits:**
- Close other Chrome tabs
- Use `--only-categories=performance` for faster results
- Add `--form-factor=desktop` for desktop audits

---

## 🎬 Live Demo Commands

**For your presentation, use these reliable commands:**

```bash
# Basic demo
npx lighthouse@10.4.0 https://google.com --only-categories=performance

# Show results in browser
npx lighthouse@10.4.0 https://google.com --only-categories=performance --view

# Mobile vs Desktop comparison
npx lighthouse@10.4.0 https://google.com --only-categories=performance --form-factor=mobile
npx lighthouse@10.4.0 https://google.com --only-categories=performance --form-factor=desktop

# Multiple categories
npx lighthouse@10.4.0 https://google.com --only-categories=performance,accessibility --view
```

---

## Quick Reference Commands 📋

```bash
# Recommended approach - no global install needed
npx lighthouse@10.4.0 https://example.com --only-categories=performance

# With browser view
npx lighthouse@10.4.0 https://example.com --only-categories=performance --view

# JSON output
npx lighthouse@10.4.0 https://example.com --only-categories=performance --output=json --output-path=report.json

# Mobile audit
npx lighthouse@10.4.0 https://example.com --only-categories=performance --form-factor=mobile

# Multiple categories
npx lighthouse@10.4.0 https://example.com --only-categories=performance,accessibility,seo

# Desktop audit
npx lighthouse@10.4.0 https://example.com --only-categories=performance --form-factor=desktop
```

---

## Key Takeaways for Your Audience 🎯

1. **Use npx instead of global installation** - Avoids 90% of setup issues
2. **Start with performance category only** - Faster results, easier to understand
3. **Pin the version** - `@10.4.0` ensures consistency
4. **Chrome DevTools is perfect for beginners** - No setup required
5. **Node.js integration for automation** - Great for CI/CD pipelines

## Next Episode Preview 🎯
In Episode 3, we'll dive deep into understanding Lighthouse performance scores and metrics - what they mean and how to interpret them for actionable insights!