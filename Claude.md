# Czech Economy AI Bot - Claude Code Instructions

## Přehled projektu
AI aplikace pro automatické generování příspěvků o české ekonomice. Každé ráno v 9:00 projde zajímavé články o ekonomice ČR, vybere nejzajímavější a vytvoří z něj příspěvek na sociální síť.

## Architektura
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

## Workflow
1. **Scraper** - Stahuje články z českých ekonomických portálů
2. **Analyzer** - AI ohodnotí relevanci a zajímavost článků
3. **Generator** - Vytvoří optimalizované příspěvky pro sociální síť
4. **Publisher** - Publikuje na Facebook, Twitter, LinkedIn
5. **Scheduler** - Orchestruje celý proces každé ráno v 9:00

## Zdroje článků
- iDNES Ekonomika
- E15.cz
- Hospodářské noviny
- ČTK (ekonomické zprávy)
- Czech News Center

## AI Integration
- **Primary**: Claude API pro analýzu a generování
- **Backup**: OpenAI GPT-4
- **Criteria**: Relevance pro ČR, aktualnost, důležitost, virální potenciál

## Social Media APIs
- Facebook Graph API (Facebook/Instagram)
- Twitter API v2
- LinkedIn API

## Deployment
- **Scheduler**: Cron job (0 9 * * *)
- **Hosting**: Linux server nebo cloud (AWS/Azure)
- **Monitoring**: Logs + email notifikace

## Development Guidelines
- Maximální délka skriptu: 100 řádků
- Modulární design pro snadnou údržbu
- Comprehensive error handling
- Konfigurace přes YAML soubory
- Logování všech operací

## Next Steps
1. Implementovat scraper.py
2. Nastavit Claude API integration
3. Vytvořit Facebook API publisher
4. Implementovat scheduler logiku
5. Setup cron job

## Claude Code Commands
```bash
# Spuštění manuálního testu
python src/scheduler.py --test

# Analýza konkrétního článku
python src/analyzer.py --url "https://..."

# Generování příspěvku z článku
python src/generator.py --article-id 123

# Test publikování
python src/publisher.py --test --platform facebook
```