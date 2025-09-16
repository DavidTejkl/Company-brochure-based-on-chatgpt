<!-- @format -->

Hlavním cílem tohoto projektu je zjednodušit proces vytváření firemních brožur. Místo ručního procházení webových stránek a kopírování informací, tento nástroj automaticky navštíví zadanou URL adresu, extrahuje relevantní obsah a poté s pomocí jazykového modelu (LLM) vytvoří souhrnnou brožuru.

## Klíčové komponenty a jak fungují

Projekt je napsán v Pythonu a využívá několik knihoven a konceptů, které jsou běžné v oblasti web scraping a zpracování přirozeného jazyka (NLP).

### 1. `project_company_brochure_chatGPT.ipynb` (Jupyter Notebook)

Toto je hlavní soubor projektu, který obsahuje veškerý kód. Jupyter Notebook je skvělý pro experimentování a iterativní vývoj, protože umožňuje spouštět kód po částech a okamžitě vidět výsledky.

#### Importy a inicializace

Na začátku notebooku jsou importy potřebných knihoven:

- `os`, `requests`, `json`, `typing`, `dotenv`, `bs4`, `IPython.display`, `openai`.
- `dotenv` se používá pro načítání API klíče z `.env` souboru, což je dobrá praxe pro zabezpečení citlivých informací.
- `OpenAI` knihovna je klíčová pro interakci s jazykovými modely.

#### Třída `Website`

Tato třída je srdcem web scraping části projektu.

- **`__init__(self, url)`**: Konstruktor třídy, který přijímá URL adresu.
  - Používá `requests` k získání obsahu webové stránky.
  - `BeautifulSoup` (`bs4`) je použita k parsování HTML obsahu.
  - Extrahuje titulek stránky (`self.title`).
  - Odstraňuje irelevantní elementy (skripty, styly, obrázky, inputy) z těla stránky, aby se získal čistý textový obsah (`self.text`).
  - Sbírá všechny odkazy (`<a>` tagy) z webové stránky (`self.links`).
- **`get_contents(self)`**: Metoda, která vrací formátovaný řetězec s titulkem a textovým obsahem webové stránky.

#### Systémové výzvy (System Prompts) pro LLM

Projekt definuje dvě hlavní systémové výzvy pro jazykové modely:

- **`link_system_prompt`**: Tato výzva instruuje LLM, aby z daného seznamu odkazů vybral ty, které jsou relevantní pro firemní brožuru (např. "O nás", "Kariéra"). Očekává se odpověď ve formátu JSON.
- **`system_prompt`**: Tato výzva instruuje LLM, aby z poskytnutého obsahu webových stránek vytvořil krátkou firemní brožuru v češtině, zaměřenou na potenciální zákazníky, investory a uchazeče o zaměstnání.

#### Funkce pro interakci s LLM

- **`get_links_user_prompt(website)`**: Generuje uživatelskou výzvu pro LLM, která obsahuje seznam odkazů z dané webové stránky a žádá o výběr relevantních odkazů.
- **`get_links(url)`**: Tato funkce vezme URL, vytvoří objekt `Website`, získá odkazy a poté zavolá LLM s `link_system_prompt` a `get_links_user_prompt` k identifikaci relevantních odkazů. Výsledek je parsován z JSON.
- **`get_all_details(url)`**: Klíčová funkce, která shromažďuje veškerý obsah pro brožuru.
  - Získá obsah úvodní stránky.
  - Použije `get_links` k nalezení relevantních podstránek.
  - Pro každou relevantní podstránku (např. "O nás", "Kariéra") stáhne její obsah a přidá ho k celkovému textu.
- **`get_brochure_user_prompt(company_name, url)`**: Generuje finální uživatelskou výzvu pro LLM, která obsahuje název společnosti a veškerý shromážděný text z webových stránek. Text je zkrácen na maximálně 5000 znaků, aby se vešel do limitů tokenů LLM.
- **`create_brochure(company_name, url)`**: Tato funkce vezme název společnosti a URL, zavolá LLM s `system_prompt` a `get_brochure_user_prompt` a zobrazí výslednou brožuru v Markdown formátu.
- **`stream_brochure(company_name, url)`**: Podobná funkci `create_brochure`, ale využívá streamování odpovědi z LLM, což umožňuje zobrazovat brožuru postupně, jak je generována. To zlepšuje uživatelský zážitek u delších generací.

## Jak to spustit?

1.  **Naklonujte si repozitář**

2.  **Nainstalujte závislosti:** Ujistěte se, že máte nainstalované všechny potřebné knihovny.

3.  **Nastavte API klíč:** Vytvořte soubor `.env` v kořenovém adresáři projektu a přidejte do něj svůj OpenAI API klíč:

    ```
    OPENAI_API_KEY="api_klíč_zde"
    ```

    Nezapomeňte, že váš API klíč by měl začínat `sk-proj-`.

4.  **Spusťte Jupyter Notebook:** Otevřete `project_company_brochure_chatGPT.ipynb` v Jupyter Lab nebo VS Code a spusťte buňky postupně.
