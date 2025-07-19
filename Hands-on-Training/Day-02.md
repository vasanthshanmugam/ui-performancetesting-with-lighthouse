Installing Lighthouse - Step by Step Guide
Method 1: CLI Installation 🖥️
Prerequisites

Node.js (version 16 or higher)
npm package manager
Google Chrome browser

Step-by-Step Installation

Check Node.js version
bashnode --version
Should show v16.0.0 or higher
Verify npm is working
bashnpm --version

Test Lighthouse without global installation (Recommended)
bashnpx lighthouse@10.4.0 https://google.com --only-categories=performance
This downloads and runs Lighthouse without installing it globally
If you prefer global installation (Optional)
bashnpm install -g lighthouse
Note: If using nvm (Node Version Manager), global installations can cause issues. Stick with npx.

Recommended CLI Usage (Using npx)
bash# Basic performance audit
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
Why npx is Better Than Global Installation
✅ Advantages of npx:

No global installation conflicts
Works with nvm (Node Version Manager)
Always uses the specified version
No permission issues
Cleaner development environment

❌ Global installation problems:

Path conflicts with nvm
Version management issues
Permission problems on some systems
Chrome connection failures


Method 2: Chrome DevTools (Built-in) 🌐
Step-by-Step Process

Open Chrome Browser

Ensure you're using Google Chrome (not Edge, Firefox, or other browsers)


Navigate to your website
https://google.com

Open Developer Tools

Windows/Linux: F12 or Ctrl + Shift + I
Mac: Cmd + Option + I
Right-click: Select "Inspect"


Find the Lighthouse tab

Look for "Lighthouse" in the top tab bar
If not visible, click the >> arrow to see more tabs


Configure your audit

Device: Choose Mobile or Desktop
Categories: Select what to audit

✅ Performance (recommended to start)
✅ Accessibility
✅ Best Practices
✅ SEO
✅ Progressive Web App




Run the audit

Click "Analyze page load"
Wait for the audit to complete (30-60 seconds)


View your results

Review scores and recommendations
Expand sections for detailed insights



DevTools Pro Tips

Clear cache: Check "Clear storage" before running
Throttling: DevTools simulates slow 3G by default
Multiple runs: Run 3-5 times and average the scores
Incognito mode: Use for consistent results without extensions
