# i18n

> [!Wikipedia Definition] i18n - Internationalization and Localization
> In [computing](https://en.wikipedia.org/wiki/Computing "Computing"), **internationalization and localization** ([American](https://en.wikipedia.org/wiki/American_English "American English")) or **internationalisation and localisation** (British), often abbreviated **i18n** and **l10n** respectively, are means of adapting to different languages, regional peculiarities and technical requirements of a target locale.
> Internationalization is the process of designing a software application so that it can be adapted to various languages and regions without engineering changes. Localization is the process of adapting internationalized software for a specific region or language by translating text and adding locale-specific components.
> Localization (which is potentially performed multiple times, for different locales) uses the infrastructure or flexibility provided by internationalization (which is ideally performed only once before localization, or as an integral part of ongoing development).[[1]](https://en.wikipedia.org/wiki/Internationalization_and_localization#cite_note-1)

# Code Snippets and File Structures
- ## i18n from `Astro Code Theme`
```
___ src
|-|  |
|_|___i18n____
|_| - nav.ts |_______ i18n: folder stores internationilzation constants
|_| - ui.ts  |
|_|----------`
|_|  |
|_|___util_____
|_| - i18n.ts |______ util: utility functions w/ translation & 
|-|...........|______ localization functions file 
|_|...........|
|_|-----------`
|_|  |
|_| - consts.ts  ______ consts: constants for i18n and site info
|--------------------------------------------------------------------|
| - astro.config.ts _____ Astro config file: Has i18n locals & lang

```


## `i18n` Folder
- #### `src/i18n/nav.ts` Navigation Bar Translations
```typescript
/**
 * This configures the navigation bar and footer. Each entry is a nav link with
 * the correct translation and url path.
 *
 * All languages will follow this ordering/structure and will fallback to the
 * default language for any entries that haven't been translated
 */
import type { SupportedLanguage } from "src/utils/i18n";


export default {
    "en": {
        "home": {
            text: "Home",
            slug: ""
        },
        "about": {
            text: "About",
            slug: "about"
        },
        "blog": {
            text: "Blog",
            slug: "/blog/pages/1"
        },
        "projects": {
            text: "Projects",
            slug: "/projects/pages/1"
        },
        "archive": {
            text: "Archive",
            slug: "archive"
        },
        "tags": {
            text: "Tags",
            slug: "tags"
        },
        "series": {
            text: "Series",
            slug: "s"
        }
    },
    "es": {
        "home": {
            text: "Página Principal",
            slug: ""
        },
        "about": {
            text: "Acerca De",
            slug: "about"
        },
        "blog": {
            text: "Blog",
            slug: "blog",
            route: "/blog/pages/1"
        },
        "projects": {
            text: "Proyectos",
            slug: "projects",
            route: "/projects/pages/1"
        },
        "archive": {
            text: "Archivo",
            slug: "archive"
        },
        "tags": {
            text: "Etiquetas",
            slug: "tags"
        },
        "series": {
            text: "Serie",
            slug: "series"
        }
    }
} as const satisfies TranslationNavEntries;


type TranslationNavEntries = Record<SupportedLanguage, Record<string, NavEntry>>


export type NavEntry = {
    /*
        Provided translation
    */
   text: string,
   /*
        Content collection slug or url for this page without the language code
   */
  slug: string,
  route?: string
};
```
- ### `i18n/ui.ts` User Interface Text Translations
```typescript
/**
 * This configures the translations for all ui text in your website. 
 * 
 * All languages will follow this ordering/structure and will fallback to the
 * default language for any entries that haven't been translated 
 */
import type { SupportedLanguage } from "src/utils/i18n";

export default {
    "en": {
        "site.title": {
            text: "Astro Theme Cody"
        },
        "site.description": {
            text: "A minimalist blog theme built with Astro. A quick and easy starter build for anyone who wants to start their own blog."
        },
        "profile.description": {
            text: "your bio description"
        },
        "blog.lastUpdated": {
            text: "Last updated:"
        },
        "sidebar.tableOfContents": {
            text: "Table of Contents"
        },
        "project.platform": {
            text: "PLATFORM"
        },
        "project.stack": {
            text: "STACK"
        },
        "project.website": {
            text: "WEBSITE"
        }
    },
    "es": {
        "site.title": {
            text: "Astro Theme Cody"
        },
        "site.description": {
            text: "Un tema de blog minimalista creado con Astro. Un tema de inicio rápido y sencillo para cualquiera que quiera crear su propio blog."
        },
        "profile.description": {
            text: "tu descripción biográfica"
        },
        "blog.lastUpdated": {
            text: "Última actualización:"
        },
        "sidebar.tableOfContents": {
            text: "Tabla de contenidos"
        },
        "project.platform": {
            text: "PLATAFORMA"
        },
        "project.stack": {
            text: "PILA"
        },
        "project.website": {
            text: "WEBSITE"
        }
    }
} as const satisfies TranslationUIEntries;

type TranslationUIEntries = Record<SupportedLanguage, Record<string, UIEntry>>;

export type UIEntry = { text: string };
```

## `util` Folder
- ### `src/util/i18n.ts` Translation and localization functions
```typescript
import { DEFAULT_LANG, SUPPORTED_LANGUAGES } from "src/consts";
import nav, { type NavEntry } from '@/i18n/nav';
import ui, { type UIEntry } from "@/i18n/ui";

export type SupportedLanguage = keyof typeof SUPPORTED_LANGUAGES;

export function getSupportedLanguages(): string[] {
    return Object.keys(SUPPORTED_LANGUAGES);
}

export function isValidLanguageCode(lang: string): boolean {
    return Object.hasOwn(SUPPORTED_LANGUAGES, lang);
}

export function getLangFromUrl(url: URL) {
    const [, lang,] = url.pathname.split('/');
    if (lang in SUPPORTED_LANGUAGES) {
        return lang as SupportedLanguage;
    }
    return DEFAULT_LANG;
}

export function getLangFromSlug(slug: string) {
    const [lang,] = slug.split('/');
    if (lang in SUPPORTED_LANGUAGES) {
        return lang as SupportedLanguage;
    }
    return DEFAULT_LANG;
}

export function getLocalizedUrl(url: URL, locale: SupportedLanguage): string {
    const [, , ...slug] = url.pathname.split('/');
    if (isValidLanguageCode(locale)) {
        return `/${locale}/${slug.join("/")}`
    }
    return `/${DEFAULT_LANG}/${slug.join("/")}`
}

export function useNavTranslations(lang: keyof typeof nav) {
    return function t(key: keyof typeof nav[SupportedLanguage]): NavEntry {
        return nav[lang][key] || nav[DEFAULT_LANG][key];
    }
}

export function useUITranslations(lang: keyof typeof nav) {
    return function t(key: keyof typeof ui[SupportedLanguage]): UIEntry {
        return ui[lang][key] || ui[DEFAULT_LANG][key];
    }
}
```

## `src/consts.ts`
- `src/consts.ts` Website Configuration File
```typescript
// This is your config file, place any global data here.
// You can import this data from anywhere in your site by using the `import` keyword.

import type nav from "./i18n/nav";
import ui from "./i18n/ui";
import type { SupportedLanguage } from "./utils/i18n";

type Config = {
  title: string;
  description: string;
  lang: string;
  profile: {
    author: string;
    description?: string;
  },
  settings: {
    paginationSize: number,
  },
}

type SocialLink = {
  icon: string;
  friendlyName: string; // for accessibility
  link: string;
}

export const SUPPORTED_LANGUAGES = {
  'en': 'en',
  'es': 'es'
};

export const DEFAULT_LANG = SUPPORTED_LANGUAGES.en as SupportedLanguage;

export const siteConfig: Config = {
  title: ui[DEFAULT_LANG]["site.title"].text,
  description: ui[DEFAULT_LANG]["site.description"].text,
  lang: DEFAULT_LANG,
  profile: {
    author: "Amy Dang",
    description: ui[DEFAULT_LANG]["profile.description"].text
  },
  settings: {
    paginationSize: 10
  }
}

/** 
  These are you social media links. 
  It uses https://github.com/natemoo-re/astro-icon#readme
  You can find icons @ https://icones.js.org/
*/
export const SOCIAL_LINKS: Array<SocialLink> = [
  {
    icon: "mdi:github",
    friendlyName: "Github",
    link: "https://github.com/kirontoo/astro-theme-cody",
  },
  {
    icon: "mdi:linkedin",
    friendlyName: "LinkedIn",
    link: "#",
  },
  {
    icon: "mdi:email",
    friendlyName: "email",
    link: "mailto:ndangamy@gmail.com",
  },
  {
    icon: "mdi:rss",
    friendlyName: "rss",
    link: "/rss.xml"
  }
];

// NOTE: match these entries with keys in `src/i18n/nav.ts`
export const NAV_LINKS: Array<keyof typeof nav[SupportedLanguage]> = [
  "home", "about", "blog", "projects", "archive"
];
```

## `astro.config.ts` Astro Config File
```typescript
import { defineConfig } from 'astro/config';
import mdx from '@astrojs/mdx';
import sitemap from '@astrojs/sitemap';
import { remarkReadingTime } from './src/utils/remark-reading-time.mjs';

import tailwind from "@astrojs/tailwind";

// https://astro.build/config
export default defineConfig({
  site: 'https://astro-theme-cody.netlify.app',
  integrations: [mdx(), sitemap(), tailwind()],
  // NOTE: Make sure this matches your supported languages in the file: src/consts.ts
  i18n: {
    defaultLocale: "en",
    locales: ["en", "es"]
  },
  markdown: {
    remarkPlugins: [remarkReadingTime],
    syntaxHighlight: 'shiki',
    shikiConfig: {
      // https://docs.astro.build/en/guides/markdown-content/#syntax-highlighting
      themes: {
        light: 'catppuccin-mocha',
        dark: 'catppuccin-latte',
      },
    }
  },
});
```

## Examples of Use 
- ## Component for Selecting Language
- `src/components/SelectLanguage.astro`
```tsx
---
import {
    getLangFromUrl,
    getLocalizedUrl,
    getSupportedLanguages,
    type SupportedLanguage,
} from "src/utils/i18n";

const { class: className } = Astro.props;

let currentLanguage = getLangFromUrl(Astro.url);
---

<lang-select>
    <label>
        <span class="sr-only">Select Language</span>
        <select
            name="languages"
            id="lang-select"
            value={currentLanguage}
            class:list={[className]}
        >
            {
                getSupportedLanguages().map((lang: string) => {
                    return (
                        <option
                            class="outline-none"
                            value={getLocalizedUrl(
                                Astro.url,
                                lang as SupportedLanguage,
                            )}
                            selected={lang === currentLanguage}
                            set:html={lang}
                        />
                    );
                })
            }
        </select>
    </label>
</lang-select>

<script>
    class LangSelect extends HTMLElement {
        constructor() {
            super();
            const select = this.querySelector("select");
            if (select) {
                select.addEventListener("change", (e) => {
                    if (e.currentTarget instanceof HTMLSelectElement) {
                        window.location.pathname = e.currentTarget.value;
                    }
                });
            }
        }
    }

    customElements.define("lang-select", LangSelect);
</script>

<style>
    select {
        @apply bg-bgColor border-b border-accent focus:border-accent focus:border;
    }
</style>
```
