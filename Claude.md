# Czech Economy AI Bot - Claude Code Instructions

## Přehled projektu
AI aplikace pro automatické generování příspěvků o české ekonomice. Každé ráno v 9:00 projde zajímavé články o ekonomice ČR, vybere nejzajímavější a vytvoří z něj příspěvek na sociální síť.

## Architektura projektu
```
czech-economy-ai-bot/
├── src/
│   ├── scraper.py       # Web scraping logika (max 100 řádků)
│   ├── analyzer.py      # AI analýza článků (max 100 řádků)  
│   ├── generator.py     # Generování příspěvků (max 100 řádků)
│   ├── publisher.py     # Publikování na sociální síť (max 100 řádků)
│   └── scheduler.py     # Hlavní orchestrace (max 100 řádků)
├── config/
│   ├── sources.yaml     # Konfigurace zdrojů
│   └── settings.py      # Nastavení API klíčů
├── data/
│   └── articles/        # Cache článků
├── logs/               # Logy aplikace
└── requirements.txt    # Python dependencies
```

## Detailní specifikace modulů

### 1. scraper.py (max 100 řádků)
**Účel**: Stahování článků z českých ekonomických portálů

**Klíčové funkce**:
- `EconomyScraper.__init__(config_path)`: Inicializace s konfigurací zdrojů
- `scrape_rss_feed(feed_url, source_name)`: Stahování z RSS feedů
- `scrape_web_page(url, source_name)`: Direct web scraping
- `_is_economic_article(title)`: Filtrování ekonomických článků
- `scrape_all_sources()`: Orchestrace stahování ze všech zdrojů

**Požadavky**:
- Použití requests + BeautifulSoup pro web scraping
- Feedparser pro RSS feeds
- Filtrování článků podle ekonomických klíčových slov
- Cache mechanismus pro duplicity
- Error handling s retry logikou
- User-Agent rotation

**Zdroje dat**:
- iDNES Ekonomika: `https://ekonomika.idnes.cz/rss.aspx`
- E15: `https://www.e15.cz/rss`
- Hospodářské noviny: `https://www.hn.cz/rss/vse`
- ČTK Ekonomika: `https://www.ceskenoviny.cz/rss/ekonomika.rss`

**Klíčová slova pro filtrování**:
ekonomika, ČNB, inflace, HDP, koruna, export, import, nezaměstnanost, mzdy, cena, banka, úrok, průmysl, obchod

### 2. analyzer.py (max 100 řádků)
**Účel**: AI analýza a hodnocení článků

**Klíčové funkce**:
- `ArticleAnalyzer.__init__(api_key)`: Inicializace Claude API
- `analyze_article(article_data)`: Analýza jednotlivého článku
- `score_articles(articles_list)`: Hodnocení seznamu článků
- `_extract_content(url)`: Extrakce obsahu článku
- `_generate_summary(content)`: AI generování shrnutí

**Hodnotící kritéria**:
- Relevance pro českou ekonomiku (0-10)
- Aktualnost a naléhavost (0-10)
- Dopad na občany (0-10)
- Virální potenciál (0-10)
- Celkové skóre (průměr + váhy)

**AI Prompt template**:
```
Analyzuj tento český ekonomický článek a ohodnoť podle kritérií:
1. Relevance pro ČR (0-10)
2. Aktualnost (0-10) 
3. Dopad na občany (0-10)
4. Virální potenciál (0-10)

Vrať JSON: {"scores": {...}, "summary": "...", "key_points": [...]}
```

### 3. generator.py (max 100 řádků)
**Účel**: Generování optimalizovaných příspěvků na sociální sítě

**Klíčové funkce**:
- `ContentGenerator.__init__(api_key)`: Inicializace AI API
- `generate_post(article, platform)`: Generování příspěvku pro konkrétní platformu
- `_get_platform_specs(platform_name)`: Specifikace platforem
- `_optimize_for_engagement()`: Optimalizace pro engagement
- `generate_variants(article, count=3)`: Generování variant příspěvků

**Platformy a specifikace**:
- **Facebook**: 500 znaků, hashtags na konci, call-to-action
- **Twitter/X**: 280 znaků, 2-3 hashtags, mention handles
- **LinkedIn**: 1300 znaků, profesionální tón, industry hashtags

**AI Prompt templates**:
```
Facebook post:
Vytvoř angažující Facebook příspěvek (max 500 znaků) o tomto ekonomickém tématu.
Použij: emoji, otázku pro engagement, 2-3 hashtags, call-to-action.
Cílová skupina: Čeští občané zajímající se o ekonomiku.

Twitter post: 
Vytvoř výstižný tweet (max 280 znaků).
Zahrnuj: klíčová čísla, 2-3 hashtags, @mention pokud relevantní.

LinkedIn post:
Profesionální příspěvek (max 1300 znaků) pro business komunitu.
Zahrnuj: kontext, implications, call for discussion.
```

### 4. publisher.py (max 100 řádků)
**Účel**: Publikování příspěvků na sociální sítě

**Klíčové funkce**:
- `SocialPublisher.__init__(config)`: Inicializace API credentials
- `publish_to_facebook(content, image_url=None)`: Facebook publikování
- `publish_to_twitter(content, image_url=None)`: Twitter publikování  
- `publish_to_linkedin(content, image_url=None)`: LinkedIn publikování
- `_handle_api_errors(response)`: Error handling pro API volání

**API Integrace**:
- **Facebook Graph API**: Používat Business API, nutný App Review
- **Twitter API v2**: Essential access, tweet posting
- **LinkedIn API**: Developer access pro company pages

**Error handling**:
- Rate limiting (exponential backoff)
- API quota monitoring
- Fallback mechanisms
- Notification při selhání

### 5. scheduler.py (max 100 řádků)
**Účel**: Hlavní orchestrace celého procesu

**Klíčové funkce**:
- `main()`: Hlavní workflow
- `run_daily_cycle()`: Denní cyklus stahování a publikování
- `_setup_logging()`: Konfigurace logování
- `_send_notification(message, level)`: Email notifikace
- `_cleanup_old_data()`: Čištění starých dat

**Workflow**:
1. Inicializace komponent
2. Stahování článků (scraper)
3. Analýza a hodnocení (analyzer)
4. Výběr nejlepšího článku
5. Generování příspěvků (generator)
6. Publikování (publisher)
7. Logování a cleanup

## Konfigurační soubory

### config/sources.yaml
```yaml
idnes_ekonomika:
  type: rss
  url: https://ekonomika.idnes.cz/rss.aspx
  encoding: utf-8
  
e15:
  type: rss
  url: https://www.e15.cz/rss
  encoding: utf-8

hospodarske_noviny:
  type: web
  url: https://www.hn.cz/ekonomika
  selectors:
    article_links: 'a.article-link'
    title: 'h2.article-title'
```

### config/settings.py
```python
import os
from dotenv import load_dotenv

load_dotenv()

# AI API
CLAUDE_API_KEY = os.getenv('CLAUDE_API_KEY')
OPENAI_API_KEY = os.getenv('OPENAI_API_KEY')  # backup

# Social Media APIs
FACEBOOK_ACCESS_TOKEN = os.getenv('FACEBOOK_ACCESS_TOKEN')
FACEBOOK_PAGE_ID = os.getenv('FACEBOOK_PAGE_ID')
TWITTER_BEARER_TOKEN = os.getenv('TWITTER_BEARER_TOKEN')
LINKEDIN_ACCESS_TOKEN = os.getenv('LINKEDIN_ACCESS_TOKEN')

# Email notifications
SMTP_SERVER = os.getenv('SMTP_SERVER', 'smtp.gmail.com')
SMTP_PORT = int(os.getenv('SMTP_PORT', 587))
EMAIL_USER = os.getenv('EMAIL_USER')
EMAIL_PASS = os.getenv('EMAIL_PASS')
NOTIFY_EMAIL = os.getenv('NOTIFY_EMAIL')

# Scheduling
SCRAPE_HOUR = 9  # 9:00 AM
TIMEZONE = 'Europe/Prague'
```

## Dependencies (requirements.txt)
```
requests>=2.31.0
beautifulsoup4>=4.12.0
feedparser>=6.0.10
pyyaml>=6.0
python-dotenv>=1.0.0
schedule>=1.2.0
anthropic>=0.18.0  # Claude API
openai>=1.12.0     # Backup AI
facebook-sdk>=3.1.0
tweepy>=4.14.0
linkedin-api>=2.0.0
python-dateutil>=2.8.2
pytz>=2023.3
```

## Error Handling Guidelines

### Logging Struktura
```python
# Používat strukturované logování
logger = logging.getLogger(__name__)
logger.info("Article scraped", extra={
    "source": source_name,
    "article_count": len(articles),
    "timestamp": datetime.now().isoformat()
})
```

### Retry Mechanismus
```python
from functools import wraps
import time

def retry(max_attempts=3, delay=1):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts - 1:
                        raise
                    time.sleep(delay * (2 ** attempt))
        return wrapper
    return decorator
```

## Testing Guidelines

### Unit Tests
- Každý modul musí mít testy
- Mock external API calls
- Test error scenarios
- Minimum 70% code coverage

### Integration Tests
- Test complete workflow
- Test API integrations s sandbox accounts
- Test cron job execution

### Manual Testing Commands
```bash
# Test scrapingu
python src/scraper.py --test --source idnes

# Test analýzy
python src/analyzer.py --test --article-url "https://..."

# Test generování
python src/generator.py --test --platform facebook

# Test publikování (dry run)
python src/publisher.py --test --dry-run

# Úplný test workflow
python src/scheduler.py --test --dry-run
```

## Deployment

### Cron Job Setup
```bash
# Editovat crontab
crontab -e

# Přidat řádek pro spuštění každé ráno v 9:00
0 9 * * * cd /path/to/czech-economy-ai-bot && python src/scheduler.py >> logs/cron.log 2>&1
```

### Environment Variables
```bash
# Vytvořit .env soubor
CLAUDE_API_KEY=sk-ant-...
FACEBOOK_ACCESS_TOKEN=...
FACEBOOK_PAGE_ID=...
TWITTER_BEARER_TOKEN=...
EMAIL_USER=your-email@gmail.com
EMAIL_PASS=your-app-password
NOTIFY_EMAIL=admin@yourdomain.com
```

### Monitoring
- Daily email reports
- Error notifications
- Performance metrics
- API quota monitoring

## Claude Code Implementation Commands

```bash
# Inicializace projektu
claude code init

# Implementace jednotlivých modulů
claude code implement src/scraper.py --template web-scraper --max-lines 100
claude code implement src/analyzer.py --template ai-analyzer --max-lines 100
claude code implement src/generator.py --template content-generator --max-lines 100
claude code implement src/publisher.py --template social-publisher --max-lines 100
claude code implement src/scheduler.py --template task-scheduler --max-lines 100

# Konfigurace
claude code generate config/sources.yaml --template yaml-config
claude code generate config/settings.py --template python-settings

# Testing
claude code test --all-modules
claude code test --integration

# Deployment
claude code deploy --platform server --cron-schedule "0 9 * * *"
```

## Success Metrics
- Denní publikování: 95%+ úspěšnost
- Relevance článků: 8/10 průměrné skóre
- Engagement rate: měřit likes, shares, comments
- API uptime: 99%+ dostupnost
- Error rate: <5% failed operations

## Rozšíření v budoucnu
- Více jazyků (slovenština)
- Video content generování
- A/B testing příspěvků
- Analytics dashboard
- Personalizace podle audience
- Instagram Stories integration