# 🍽️ Ricettario PWA

Webapp installabile come app nativa su telefono e desktop.

---

## 📁 File inclusi

| File | Descrizione |
|------|-------------|
| `index.html` | L'intera app (React + stili + logica) |
| `manifest.json` | Configurazione PWA (nome, icone, colori) |
| `sw.js` | Service worker per funzionamento offline |
| `icon-192.png` | Icona app 192×192 (da aggiungere) |
| `icon-512.png` | Icona app 512×512 (da aggiungere) |

---

## 🚀 Deploy gratuito su Netlify (consigliato, 2 minuti)

1. Vai su **[netlify.com](https://netlify.com)** e crea un account gratuito
2. Trascina l'intera cartella `ricettario-pwa` nella pagina "Sites"
3. Netlify ti darà un URL tipo `https://mio-ricettario.netlify.app`
4. Fatto!

---

## 📱 Installare sul telefono (iOS / Android)

### iPhone / iPad
1. Apri l'URL in **Safari** (non Chrome)
2. Tocca l'icona **Condividi** (□↑)
3. Scorri e tocca **"Aggiungi alla schermata Home"**
4. L'app appare come un'icona nativa

### Android
1. Apri l'URL in **Chrome**
2. Tocca i tre puntini in alto a destra
3. Tocca **"Aggiungi a schermata Home"** o **"Installa app"**

---

## 🖥️ Installare su Mac / Windows / Linux

1. Apri l'URL in **Chrome** o **Edge**
2. Clicca l'icona di installazione nella barra degli indirizzi (⊕ o computer con freccia)
3. Clicca **"Installa"**
4. L'app si apre in una finestra dedicata senza browser

---

## 💾 Dove vengono salvati i dati?

### Modalità base (offline)
I dati sono salvati nel **localStorage** del browser/app sul tuo dispositivo.
- Persistono tra sessioni: chiudi e riapri, le ricette ci sono
- Sono locali: non vanno su nessun server

### Modalità cloud (consigliata): Supabase + magic link
Se configuri Supabase, le ricette vengono salvate **anche su cloud** e sono disponibili su più dispositivi (stesso account email).

#### Setup rapido Supabase (gratuito)
1. Crea un progetto su Supabase
2. Vai su **Project Settings → API** e copia:
   - `URL`
   - `anon public key`
3. Apri `index.html` e imposta:
   - `SUPABASE_URL`
   - `SUPABASE_ANON_KEY`

#### SQL (crea tabella + policy)
In Supabase apri **SQL Editor** e incolla:

```sql
create table if not exists public.recipes (
  user_id uuid not null default auth.uid(),
  client_id text not null,
  data jsonb not null,
  updated_at timestamptz not null default now(),
  deleted boolean not null default false,
  primary key (user_id, client_id)
);

alter table public.recipes enable row level security;

create policy "recipes_select_own" on public.recipes
  for select using (auth.uid() = user_id);

create policy "recipes_upsert_own" on public.recipes
  for insert with check (auth.uid() = user_id);

create policy "recipes_update_own" on public.recipes
  for update using (auth.uid() = user_id) with check (auth.uid() = user_id);
```

#### Auth: magic link
In Supabase vai su **Authentication → Providers → Email** e abilita:
- Email provider attivo
- OTP / Magic link abilitato (impostazione equivalente, a seconda della UI)

#### Sync
- Dopo login: l'app fa **pull** dal cloud e unisce con il locale.
- Ad ogni modifica: salva subito in locale e fa un **push** al cloud (con piccolo debounce).

---

## 🎨 Aggiungere le icone (opzionale)

Crea due immagini PNG con il logo dell'app:
- `icon-192.png` — 192×192 px
- `icon-512.png` — 512×512 px

Puoi usare qualsiasi emoji o logo. Senza icone l'app funziona ugualmente ma mostrerà un'icona generica.

Un'icona veloce: vai su [favicon.io/emoji-favicons](https://favicon.io/emoji-favicons/), cerca "fork and knife", scarica e rinomina i file.
