# Pomodoro Timer 🍅

Aplikasi Pomodoro Timer untuk meningkatkan produktivitas, dibuat dengan Alpine.js dan Tailwind CSS.

Project reference: [roadmap.sh/projects/pomodoro-timer](https://roadmap.sh/projects/pomodoro-timer)

---

## Demo

**Live:** https://pomodoro.amirkurniawan.store *(jika sudah deploy)*

---

## Screenshot

```
┌─────────────────────────────────────┐
│         🍅 Pomodoro Timer           │
│      Stay focused, be productive    │
│                                     │
│  [Pomodoro] [Short Break] [Long]    │
│                                     │
│          ╭─────────────╮            │
│         │              │            │
│         │    24:59     │            │
│         │  Focus Time  │            │
│          ╰─────────────╯            │
│                                     │
│      [ START ]    [ ⟲ ]             │
│                                     │
│      Sessions: ● ● ○ ○  #2          │
│                                     │
│           ⚙️ Settings               │
└─────────────────────────────────────┘
```

---

## Features

| Feature | Description |
|---------|-------------|
| ▶️ Start/Pause/Reset | Kontrol timer lengkap |
| 🍅 Pomodoro Mode | 25 menit fokus kerja |
| ☕ Short Break | 5 menit istirahat |
| 🛋️ Long Break | 15 menit istirahat (setelah 4 sesi) |
| ⚙️ Configurable | Ubah durasi sesuai kebutuhan |
| 🔔 Sound Alert | Notifikasi suara saat selesai |
| 📊 Session Counter | Track jumlah sesi kerja |
| 💾 Persistent | Settings tersimpan di localStorage |
| 📱 Responsive | Mobile & desktop friendly |
| 🎨 Dynamic Colors | Warna berubah sesuai mode |

---

## Tech Stack

| Technology | Purpose |
|------------|---------|
| Alpine.js | Reactive JavaScript framework |
| Tailwind CSS | Utility-first CSS framework |
| LocalStorage | Persist settings & sessions |
| Web Audio API | Sound notifications |

---

## How It Works

```
┌─────────────────────────────────────────────────────────────┐
│                   POMODORO TECHNIQUE                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   🍅 WORK        25 min  ─┐                                │
│   ☕ SHORT BREAK  5 min   │                                │
│   🍅 WORK        25 min   ├─► Repeat 4x                    │
│   ☕ SHORT BREAK  5 min   │                                │
│   🍅 WORK        25 min   │                                │
│   ☕ SHORT BREAK  5 min   │                                │
│   🍅 WORK        25 min  ─┘                                │
│   🛋️ LONG BREAK  15 min  ─► Then long break               │
│                                                             │
│   Cycle repeats...                                          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Project Structure

```
pomodoro-timer/
├── index.html      # Single file application
└── README.md       # Documentation
```

**No build step required!** Semua dalam 1 file HTML.

---

## Quick Start

### Local Development

```bash
# Clone or download
cd pomodoro-timer

# Open in browser
open index.html
# atau
python -m http.server 8000
# lalu buka http://localhost:8000
```

---

## Deploy ke Server (Nginx)

### Opsi 1: Port 80 Belum Digunakan (Simple)

```bash
# Copy file ke web root
sudo mkdir -p /var/www/pomodoro
sudo cp index.html /var/www/pomodoro/

# Buat Nginx config
sudo nano /etc/nginx/sites-available/pomodoro
```

Isi config:
```nginx
server {
    listen 80;
    server_name _;

    root /var/www/pomodoro;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Enable & reload:
```bash
sudo ln -s /etc/nginx/sites-available/pomodoro /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

### Opsi 2: Port 80 Sudah Digunakan Site Lain (Pakai Port Berbeda)

Jika port 80 sudah digunakan (misal untuk site utama), gunakan port lain seperti **8081**.

```bash
# Copy file
sudo mkdir -p /var/www/pomodoro
sudo cp index.html /var/www/pomodoro/

# Buat Nginx config
sudo nano /etc/nginx/sites-available/pomodoro
```

Isi config dengan **port 8081**:
```nginx
server {
    listen 8081;
    listen [::]:8081;

    server_name _;

    root /var/www/pomodoro;
    index index.html;

    access_log /var/log/nginx/pomodoro.access.log;
    error_log /var/log/nginx/pomodoro.error.log;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Enable & reload:
```bash
sudo ln -s /etc/nginx/sites-available/pomodoro /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

Test akses lokal:
```bash
curl localhost:8081
```

Di **Cloudflare Tunnel**, arahkan ke port 8081:

| Subdomain | Domain | Type | URL |
|-----------|--------|------|-----|
| `pomodoro` | yourdomain.store | HTTP | `localhost:8081` |

---

### Opsi 3: Virtual Host (Recommended) ⭐

Semua site tetap di **port 80**, Nginx routing berdasarkan **hostname**.

#### Step 1: Update config site yang sudah ada

```bash
sudo nano /etc/nginx/sites-available/google-kw
```

Pastikan `server_name` sesuai domain:
```nginx
server {
    listen 80;
    listen [::]:80;

    server_name yourdomain.store;  # <-- Sesuaikan domain

    root /var/www/google-kw;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

#### Step 2: Buat config untuk Pomodoro

```bash
sudo mkdir -p /var/www/pomodoro
sudo cp index.html /var/www/pomodoro/
sudo nano /etc/nginx/sites-available/pomodoro
```

Isi config dengan **subdomain**:
```nginx
server {
    listen 80;
    listen [::]:80;

    server_name pomodoro.yourdomain.store;  # <-- Subdomain

    root /var/www/pomodoro;
    index index.html;

    access_log /var/log/nginx/pomodoro.access.log;
    error_log /var/log/nginx/pomodoro.error.log;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

#### Step 3: Enable & reload

```bash
sudo ln -s /etc/nginx/sites-available/pomodoro /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

#### Step 4: Test di Localhost

Karena semua site di port 80, gunakan **Host header** untuk test:

```bash
# Test pomodoro
curl -H "Host: pomodoro.yourdomain.store" http://localhost

# Test site utama
curl -H "Host: yourdomain.store" http://localhost
```

**Atau test via browser** — edit `/etc/hosts`:

```bash
sudo nano /etc/hosts
```

Tambahkan:
```
127.0.0.1   pomodoro.yourdomain.store
127.0.0.1   yourdomain.store
```

Lalu buka di browser: `http://pomodoro.yourdomain.store`

> **Note:** Hapus entry di `/etc/hosts` setelah selesai test, agar tidak conflict dengan DNS asli.

#### Step 5: Cloudflare Tunnel

Kedua hostname tetap arahkan ke **port 80**:

| Hostname | Type | URL |
|----------|------|-----|
| `yourdomain.store` | HTTP | `localhost:80` |
| `pomodoro.yourdomain.store` | HTTP | `localhost:80` |

Nginx akan otomatis routing berdasarkan hostname yang diminta.

---

### Perbandingan Opsi

| Opsi | Port | Kelebihan | Kekurangan |
|------|------|-----------|------------|
| **1. Simple** | 80 | Mudah setup | Hanya untuk 1 site |
| **2. Port Berbeda** | 8081, 8082, dst | Mudah, jelas terpisah | Harus ingat port masing-masing |
| **3. Virtual Host** ⭐ | 80 (semua) | Professional, scalable | Perlu config per subdomain |

**Rekomendasi:** Gunakan **Opsi 3 (Virtual Host)** untuk setup yang lebih clean dan scalable.

---

## Customization

### Default Settings

```javascript
settings: {
    work: 25,        // minutes
    shortBreak: 5,   // minutes
    longBreak: 15,   // minutes
    soundEnabled: true
}
```

### Colors (Tailwind Config)

```javascript
colors: {
    'pomodoro': '#e74c3c',      // Red - Work mode
    'short-break': '#27ae60',   // Green - Short break
    'long-break': '#3498db',    // Blue - Long break
}
```

### Change Alarm Sound

Edit audio source di HTML:
```html
<audio id="alarmSound" preload="auto">
    <source src="YOUR_SOUND_URL.mp3" type="audio/mpeg">
</audio>
```

---

## Keyboard Shortcuts (Future Enhancement)

| Key | Action |
|-----|--------|
| `Space` | Start/Pause |
| `R` | Reset |
| `1` | Switch to Pomodoro |
| `2` | Switch to Short Break |
| `3` | Switch to Long Break |

---

## Browser Support

- ✅ Chrome
- ✅ Firefox
- ✅ Safari
- ✅ Edge
- ✅ Mobile browsers

---

## localStorage Data

```javascript
// Settings
localStorage.getItem('pomodoroSettings')
// {"work":25,"shortBreak":5,"longBreak":15,"soundEnabled":true}

// Sessions completed
localStorage.getItem('pomodoroSessions')
// "8"
```

---

## Accessibility

- ✅ Keyboard navigable
- ✅ Screen reader friendly
- ✅ High contrast colors
- ✅ Clear visual feedback
- ✅ Page title updates with timer

---

## What I Learned

1. **Alpine.js** - Lightweight reactive framework
2. **Tailwind CSS** - Utility-first styling
3. **SVG Animation** - Circular progress ring
4. **Web APIs** - Audio, Notifications, localStorage
5. **State Management** - Timer logic & session tracking

---

## References

- [Alpine.js Documentation](https://alpinejs.dev/)
- [Tailwind CSS Documentation](https://tailwindcss.com/)
- [Pomodoro Technique](https://en.wikipedia.org/wiki/Pomodoro_Technique)
- [Pomofocus](https://pomofocus.io/) - Inspiration

---

## License

MIT

---

*Stay focused, be productive! 🍅*
