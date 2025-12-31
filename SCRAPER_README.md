# LinkedIn Multi-Profile Scraper

A production-grade Python tool for scraping LinkedIn company and personal profiles using Selenium with headless browser automation. Runs completely in the background with no visible UI.

## Features

✅ **Headless Browser Automation** - No visible window, runs entirely in background  
✅ **Multi-Profile Support** - Scrape multiple profiles in a single run  
✅ **Company & Personal Profiles** - Support for both company pages and personal profiles  
✅ **Rich Post Data** - Extracts content, metrics, hashtags, media info  
✅ **Real Browser Login** - Uses actual browser automation, no cookie workarounds needed  
✅ **Robust Deduplication** - Prevents duplicate posts across scraping sessions  
✅ **Smart Date Parsing** - Converts LinkedIn relative dates (2d, 3w) to actual dates  
✅ **Excel Export** - Formatted output with profiles and posts in structured format  
✅ **Error Handling** - Comprehensive logging and error recovery  

## What It Scrapes

For each profile:
- Profile name, headline, location, followers/connections count
- Profile summary/bio and profile URL
- Profile type (Personal/Company)

For each post:
- Post content (full text)
- Post date and URL
- Engagement metrics (likes, comments, shares)
- Content type (Text, Image, Video, Document)
- Hashtags and mentions
- Engagement rates and ratios
- Word count and character count

## Requirements

- Python 3.10+
- Chrome/Chromium browser installed
- LinkedIn account with login credentials
- Active internet connection

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/linkedin-scraper.git
cd linkedin-scraper
```

### 2. Create Virtual Environment

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Verify Chrome is Installed

The scraper uses WebDriver Manager to automatically download the correct ChromeDriver version:

```bash
python -c "from webdriver_manager.chrome import ChromeDriverManager; print(ChromeDriverManager().install())"
```

## Configuration

### Update Credentials

Edit `linkedin_scraper.py` and update the configuration section:

```python
LINKEDIN_EMAIL = "your-email@example.com"
LINKEDIN_PASSWORD = "your-password"

PROFILES_TO_SCRAPE = [
    "https://www.linkedin.com/company/company-name/",
    "https://www.linkedin.com/in/person-name/",
    # Add more URLs here
]

MAX_POSTS_PER_PROFILE = 15  # Posts to extract per profile
```

### Environment Variables (Optional)

For production deployment, use environment variables instead:

```bash
export LINKEDIN_EMAIL="your-email@example.com"
export LINKEDIN_PASSWORD="your-password"
```

Then update the script to read from `os.getenv()`.

## Usage

### Basic Usage

```bash
python linkedin_scraper.py
```

The scraper will:
1. Start a headless Chrome browser (invisible)
2. Log in with your credentials
3. Navigate to each profile URL
4. Extract posts and metadata
5. Save results to Excel file
6. Close the browser
7. Print summary statistics

### Example Output

```
======================================================================
🔵 LinkedIn Multi-Profile Scraper (Headless Browser)
======================================================================

🌐 Setting up browser (headless - no window)...
✅ Browser ready (running headless in background)

🔐 Logging in as user@example.com...
✅ Login successful!

======================================================================
📍 Scraping Company profile: ikfdigital
   Original input: https://www.linkedin.com/company/ikfdigital/
======================================================================

✅ Profile: IKF Digital
   Headline: Digital Marketing Company...
   Location: Mumbai, India
   Followers: 5,432
   Followers: 128

📜 Scrolling company posts page to load all posts...
  Scroll 1/12
  Scroll 2/12
  ...

📝 Extracting posts...
📊 Found 15 UNIQUE post elements (after dedup)
  ✓ Post 1: 2024-12-25
  ✓ Post 2: 2024-12-24
  ...

✅ Extracted 15 posts from @ikfdigital

✅ SUCCESS!
   Total Profiles: 3
   Total Posts: 45
   Total Reactions: 2,341
   Total Comments: 567
   Total Engagement: 2,908

💾 Saved: linkedin_multi_profile_20251231_104903.xlsx

📈 Summary by Profile:
   @ikfdigital: 15 posts
   @amura-marketing-technologies-pvt-ltd: 15 posts
   @communicate-india: 15 posts

✅ Done!
```

## Output Format

The scraper exports a single Excel file with these columns:

| Column | Description |
|--------|-------------|
| Profile Username | LinkedIn username/company slug |
| Profile Name | Full name |
| Profile Headline | Headline/tagline |
| Profile Location | Geographic location |
| Profile URL | Direct link to profile |
| Profile Followers | Follower count |
| Profile Connections | Connection/staff count |
| Profile Summary | Bio/about section |
| Post Position | Order among extracted posts |
| Post URL | Direct link to post |
| Post ID | LinkedIn post ID |
| Post Date | Post publication date (YYYY-MM-DD) |
| Post Content (Full) | Complete post text |
| Content URL | Link to attached media |
| Post Word Count | Word count of post text |
| Content Type | Text Only / Image / Video / Document |
| Has Document | Whether post has document attachment |
| Hashtags | Comma-separated hashtags |
| Hashtag Count | Number of unique hashtags |
| Post Reactions (Likes) | Like count |
| Post Comments | Comment count |
| Post Shares | Share count |
| Total Engagement | Sum of all engagement metrics |
| Engagement Rate (%) | (Total Engagement / Followers) × 100 |
| Comment/Reaction Ratio | Comments / Reactions ratio |
| Share/Reaction Ratio | Shares / Reactions ratio |
| Scraped At | Timestamp when post was extracted |

## Architecture

```
linkedin_scraper.py
├── LinkedInMultiProfileScraper (Main Class)
│   ├── setup_driver()              # Initialize headless Chrome
│   ├── login()                     # Authenticate to LinkedIn
│   ├── scrape_profile()            # Main scraping orchestrator
│   ├── extract_profile_info()      # Get profile metadata
│   ├── extract_posts()             # Find and extract posts
│   ├── extract_single_post()       # Parse individual post data
│   ├── extract_hashtags()          # Parse hashtags from text/HTML
│   ├── convert_relative_date_to_actual()  # Parse LinkedIn date format
│   ├── extract_username_from_url() # Parse profile URL
│   ├── scroll_page()               # Load more posts via scrolling
│   └── save_to_excel()             # Export to formatted Excel
└── main()                          # CLI entry point
```

## Key Methods

### `extract_hashtags()`
- Parses hashtags from post text
- Handles concatenated hashtags (e.g., "#AI#ML")
- Extracts from HTML links and encoded formats (%23)
- Deduplicates case-insensitively

### `convert_relative_date_to_actual()`
- Converts LinkedIn relative dates to ISO format
- Handles: "2d", "3w", "1mo", "Today", "Yesterday"
- Falls back to current year for incomplete dates

### `extract_single_post()`
- Multi-selector approach for robust content extraction
- Deduplicates posts using content hash + date + position
- Extracts engagement metrics via regex and aria-labels
- Detects media type and extracts URLs
- Calculates engagement rates and ratios

## Troubleshooting

### Login Fails

**Issue**: "Login may have failed - proceeding anyway"

**Solutions**:
- Verify credentials are correct
- Check if LinkedIn requires 2FA (not supported yet)
- Try manual login to confirm account works
- Ensure no IP blocks or rate limiting

### No Posts Found

**Issue**: "Found 0 UNIQUE post elements (after dedup)"

**Solutions**:
- Profile may have no public posts
- CSS selectors may have changed (LinkedIn updates frequently)
- Try scrolling longer: increase `scrolls=` parameter
- Check browser console screenshot for JavaScript errors

### Date Parsing Issues

**Issue**: Posts show "N/A" for date or incorrect dates

**Solutions**:
- LinkedIn date format may have changed
- Try updating regex patterns in `convert_relative_date_to_actual()`
- Check `debug_{profile_name}_profile.png` screenshot

### Memory/Performance

**Issue**: Script hangs or uses excessive memory

**Solutions**:
- Reduce `MAX_POSTS_PER_PROFILE`
- Add delays between profiles: `time.sleep(5)`
- Run on a fresh browser instance: add `options.add_argument('--new-instance')`

### Chrome Driver Issues

**Issue**: "ChromeDriver not found"

**Solutions**:
```bash
# Reinstall WebDriver Manager
pip install --upgrade webdriver-manager

# Or manually specify Chrome path
from selenium.webdriver.chrome.service import Service
service = Service("/path/to/chromedriver")
```

## Best Practices

### 1. Rate Limiting
```python
time.sleep(3)  # Between profiles
time.sleep(2)  # Between scrolls
```

### 2. Multiple Runs
- Store outputs in `data/raw/` with timestamps
- Compare outputs to detect new posts
- Archive old files for historical tracking

### 3. Security
- Never commit credentials to Git
- Use environment variables for production
- Use a dedicated scraper account if possible
- Rotate passwords regularly

### 4. Maintenance
- LinkedIn updates CSS selectors periodically
- Update selectors when scraping stops working
- Log extraction errors for debugging
- Monitor LinkedIn's ToS for changes

## Limitations

⚠️ **LinkedIn Rate Limiting** - May encounter blocks after many requests  
⚠️ **JavaScript Rendering** - Some posts load dynamically (partially handled via scrolling)  
⚠️ **2FA Not Supported** - Accounts with two-factor auth will fail  
⚠️ **Relative Performance** - Slower than API (if one existed), but more reliable  
⚠️ **Post Text Truncation** - LinkedIn truncates long posts; limited extraction possible  

## Legal & Ethical Considerations

⚠️ **Terms of Service** - Scraping may violate LinkedIn's ToS  
⚠️ **Data Privacy** - Ensure compliance with GDPR, CCPA, etc.  
⚠️ **Rate Limiting** - Use responsibly to avoid account blocks  
⚠️ **Attribution** - Credit LinkedIn and creators when sharing data  

## Integration with PostgreSQL System

This scraper integrates with the production intelligence system:

```bash
# Move output to data directory
mv linkedin_multi_profile_*.xlsx data/raw/

# Import into database
python scripts/import_data.py \
  --platform linkedin \
  --file data/raw/linkedin_multi_profile_*.xlsx \
  --company "Company Name"

# Generate reports
python scripts/generate_reports.py --company "Company Name"
```

See [README.md](README.md) for full integration guide.

## Development

### Running with Debug Output

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

### Modifying Selectors

CSS selectors for LinkedIn change frequently. To debug:

1. Remove `--headless=new` to see browser
2. Use Chrome DevTools (F12) to inspect elements
3. Update selectors in relevant `extract_*` methods
4. Test with single profile first

### Adding New Platforms

The architecture supports extending to Instagram, Facebook, etc.:

```python
class InstagramScraper(BaseScraper):
    def __init__(self, username, password):
        # Implement platform-specific logic
        pass
```

## Contributing

Contributions welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Changelog

### v1.0.0 (2025-12-31)
- Initial release
- Support for company and personal profiles
- Robust post extraction and deduplication
- Excel export with formatting
- Comprehensive error handling

## Support

For issues, questions, or suggestions:
- Open an issue on GitHub
- Check existing issues for solutions
- Include error logs and screenshots

## License

MIT License - see LICENSE file for details

## Author

Your Name / Organization  
[LinkedIn Profile](https://www.linkedin.com/in/yourprofile)  
[GitHub Profile](https://github.com/yourusername)

---

**Disclaimer**: This tool is for educational and research purposes. Users are responsible for complying with LinkedIn's Terms of Service and applicable laws regarding data scraping and privacy.

