# 🚀 The Ultimate Node.js Website Building Cheatsheet

### *Because Googling the same commands 47 times is getting old*

---

## 🎬 Act I: Project Setup (a.k.a. "Let There Be Code")

### Initialize a New Project

```bash
# The "I'm starting fresh and full of hope" command
npm init -y

# The "I'm a responsible adult who fills out forms properly" version
npm init
```

**Pro tip:** The `-y` flag says "yes" to everything, like me at a pizza buffet.

### Create Project Structure

```bash
# Create all folders in one go (because we're efficient like that)
mkdir -p src/{components,pages,styles,utils,lib,hooks,assets/{images,fonts}}

# Create essential files
touch src/index.js src/app.js .env .env.example .gitignore README.md
```

### Git Setup (Version Control Your Life Choices)

```bash
# Initialize git (start tracking your mistakes... I mean, progress)
git init

# Create .gitignore before you accidentally commit node_modules
# (we've all been there, no judgment)
echo "node_modules/
.env
.DS_Store
*.log
dist/
build/
.next/
coverage/" > .gitignore

# First commit (the "it begins" moment)
git add .
git commit -m "Initial commit - hope and dreams intact"
```

---

## 📦 Act II: Package Management (a.k.a. "Dependency Hell's Waiting Room")

### NPM Commands You'll Use Daily

```bash
# Install a package (add it to your ever-growing node_modules black hole)
npm install <package-name>
npm i <package-name>              # For the lazy (respect)

# Install as dev dependency (stuff you need but users don't)
npm install -D <package-name>
npm install --save-dev <package-name>

# Install globally (use your power wisely)
npm install -g <package-name>

# Install specific version (when latest breaks everything, as it does)
npm install <package-name>@1.2.3
npm install <package-name>@latest  # YOLO mode

# Uninstall (breaking up is hard to do)
npm uninstall <package-name>
npm un <package-name>              # Even lazier

# Update packages
npm update                         # Update all the things
npm update <package-name>          # Update one specific thing

# Check for outdated packages (prepare for anxiety)
npm outdated

# Audit for vulnerabilities (prepare for MORE anxiety)
npm audit
npm audit fix                      # Let npm try to fix it
npm audit fix --force              # "I don't care anymore, just fix it"
```

### Package.json Scripts Magic

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "eslint . --ext .js,.jsx,.ts,.tsx",
    "lint:fix": "eslint . --ext .js,.jsx,.ts,.tsx --fix",
    "format": "prettier --write .",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "clean": "rm -rf node_modules .next dist build",
    "nuke": "rm -rf node_modules package-lock.json && npm install",
    "cry": "echo 'There there, it will be okay' && npm run nuke"
  }
}
```

**Run scripts with:**
```bash
npm run dev
npm run build
npm run cry   # We've all needed this
```

---

## 🏗️ Act III: Framework Installation (Choose Your Fighter)

### Next.js (The Popular Kid)

```bash
# Create new Next.js app (the fancy automatic way)
npx create-next-app@latest my-app

# With all the bells and whistles
npx create-next-app@latest my-app --typescript --tailwind --eslint --app --src-dir

# Manual installation (for control freaks, no offense)
npm install next react react-dom
```

### Express.js (The OG)

```bash
# Install Express
npm install express

# Common Express companions
npm install cors helmet morgan compression cookie-parser
npm install -D nodemon

# Express generator (scaffolding for the lazy... efficient)
npx express-generator my-app
```

### Useful Starter Commands

```bash
# Create React App (if you're old school)
npx create-react-app my-app
npx create-react-app my-app --template typescript

# Vite (for the speed demons)
npm create vite@latest my-app -- --template react
npm create vite@latest my-app -- --template react-ts
```

---

## 🎨 Act IV: Essential Packages (The Avengers of npm)

### Styling & UI

```bash
# Tailwind CSS (utility-first, sanity-second)
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# CSS-in-JS options
npm install styled-components
npm install @emotion/react @emotion/styled
npm install sass                   # For the SCSS lovers

# UI Component Libraries
npm install @radix-ui/react-*      # Headless UI primitives
npm install @headlessui/react      # Tailwind's best friend
npm install lucide-react           # Icons that don't suck
npm install framer-motion          # Make things go whoooosh
```

### Forms & Validation

```bash
# Form handling
npm install react-hook-form        # Forms without the tears
npm install formik                 # Alternative tears

# Validation
npm install zod                    # Schema validation that sparks joy
npm install yup                    # The classic
npm install joi                    # For backend validation
```

### State Management

```bash
# The lightweight options
npm install zustand                # Small but mighty
npm install jotai                  # Atomic state

# The heavyweight champion
npm install @reduxjs/toolkit react-redux

# Server state (because fetching is complicated)
npm install @tanstack/react-query  # Formerly React Query
npm install swr                    # Stale While Revalidate (fancy)
```

### Database & ORM

```bash
# Prisma (the cool ORM)
npm install prisma @prisma/client
npx prisma init
npx prisma generate
npx prisma db push                 # Yeet schema to database
npx prisma studio                  # GUI for your data

# Mongoose (MongoDB's best friend)
npm install mongoose

# Drizzle (the new kid on the block)
npm install drizzle-orm
npm install -D drizzle-kit
```

### Authentication

```bash
# NextAuth.js / Auth.js
npm install next-auth

# Clerk (auth made stupid easy)
npm install @clerk/nextjs

# JWT stuff
npm install jsonwebtoken bcryptjs
npm install -D @types/jsonwebtoken @types/bcryptjs
```

### Utilities

```bash
# Date handling (because Date() is cursed)
npm install date-fns               # Lightweight
npm install dayjs                  # Even more lightweight
npm install luxon                  # When you need power

# Data fetching
npm install axios                  # HTTP requests made nice

# Environment variables
npm install dotenv                 # Load .env files

# Validation & Sanitization
npm install validator              # String validation
npm install dompurify              # XSS protection

# Miscellaneous magic
npm install lodash                 # Utility belt
npm install uuid                   # Generate unique IDs
npm install nanoid                 # Smaller unique IDs
npm install slugify                # URL-friendly strings
npm install sharp                  # Image processing beast
```

---

## 🧪 Act V: Development & Testing (Trust Issues, Codified)

### Linting & Formatting

```bash
# ESLint (the code police)
npm install -D eslint
npx eslint --init

# Prettier (the code beautician)
npm install -D prettier eslint-config-prettier eslint-plugin-prettier

# Both playing nice together
npm install -D eslint-config-prettier
```

**Create `.prettierrc`:**
```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 80
}
```

### TypeScript Setup

```bash
# Install TypeScript
npm install -D typescript @types/node @types/react @types/react-dom

# Initialize tsconfig.json
npx tsc --init

# Type checking without compilation
npx tsc --noEmit
```

### Testing

```bash
# Jest (the testing standard)
npm install -D jest @types/jest ts-jest
npx jest --init

# React Testing Library
npm install -D @testing-library/react @testing-library/jest-dom @testing-library/user-event

# Playwright (E2E testing that actually works)
npm install -D @playwright/test
npx playwright install

# Vitest (Vite's testing buddy)
npm install -D vitest
```

### Development Tools

```bash
# Nodemon (auto-restart on changes)
npm install -D nodemon

# Concurrently (run multiple commands)
npm install -D concurrently

# Cross-env (environment variables across platforms)
npm install -D cross-env

# Husky (git hooks made easy)
npm install -D husky
npx husky init

# Lint-staged (lint only staged files)
npm install -D lint-staged
```

---

## 🚀 Act VI: Build & Deploy (Ship It!)

### Build Commands

```bash
# Next.js
npm run build                      # Build for production
npm run start                      # Start production server

# Analyze bundle (find the chonky dependencies)
npm install -D @next/bundle-analyzer
ANALYZE=true npm run build

# Check build output
npx next info                      # System info for debugging
```

### Docker Commands

```bash
# Build image
docker build -t my-app .

# Run container
docker run -p 3000:3000 my-app

# Docker Compose (for the sophisticated)
docker-compose up
docker-compose up -d               # Detached (background)
docker-compose down                # Shut it down
docker-compose logs -f             # Watch the chaos unfold
```

**Basic `Dockerfile` for Next.js:**
```dockerfile
FROM node:20-alpine AS base

FROM base AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci

FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM base AS runner
WORKDIR /app
ENV NODE_ENV production
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static

EXPOSE 3000
CMD ["node", "server.js"]
```

### Environment Variables

```bash
# Create environment files
touch .env .env.local .env.development .env.production

# Example .env structure
DATABASE_URL="postgresql://..."
NEXTAUTH_SECRET="your-secret-here"
NEXTAUTH_URL="http://localhost:3000"
```

**⚠️ NEVER commit `.env` files. I repeat, NEVER. Your secrets belong in a vault, not GitHub.**

---

## 🔧 Act VII: Debugging & Troubleshooting (a.k.a. "Why God Why")

### Common "It's Broken" Commands

```bash
# The classic fix-it sequence
rm -rf node_modules package-lock.json
npm install

# Clear all caches (the nuclear option)
npm cache clean --force
rm -rf node_modules package-lock.json .next
npm install

# Check what's eating disk space
du -sh node_modules                # Spoiler: it's a lot
npx npkill                         # Interactive node_modules cleaner

# Find zombie processes
lsof -i :3000                      # What's using port 3000?
kill -9 <PID>                      # Eliminate it

# Windows users:
netstat -ano | findstr :3000
taskkill /PID <PID> /F
```

### Debugging Tools

```bash
# Node.js debugger
node --inspect server.js
node --inspect-brk server.js       # Break on first line

# Debug with Chrome DevTools
# Open chrome://inspect in Chrome

# Environment check
node -v                            # Node version
npm -v                             # npm version
npx -v                             # npx version
which node                         # Where is node?
```

### Common Error Fixes

```bash
# "EACCES permission denied"
sudo chown -R $(whoami) ~/.npm
sudo chown -R $(whoami) /usr/local/lib/node_modules

# "ENOSPC: System limit for number of file watchers reached"
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

# "Cannot find module" (the classic)
npm install                        # Did you forget this?
rm -rf node_modules && npm install # Still broken? Try this

# Port already in use
npx kill-port 3000                 # Goodbye, zombie process
```

---

## 📊 Act VIII: Performance & SEO Commands

### Lighthouse & Performance

```bash
# Run Lighthouse from CLI
npm install -g lighthouse
lighthouse https://your-site.com --output html --output-path ./report.html

# Lighthouse CI
npm install -D @lhci/cli
npx lhci autorun

# Bundle analysis
npm install -D webpack-bundle-analyzer
npx next build && npx next-bundle-analyzer
```

### SEO Tools

```bash
# Generate sitemap
npm install next-sitemap
npx next-sitemap

# Check for broken links
npm install -g broken-link-checker
blc https://your-site.com -ro      # Recursive check

# Validate structured data
# Use Google's Rich Results Test: https://search.google.com/test/rich-results
```

---

## 🎭 Act IX: Useful One-Liners (Copy-Paste Paradise)

```bash
# Generate random secret key
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"

# Pretty print JSON file
cat file.json | node -e "process.stdin.pipe(require('stream').Transform({transform:(c,e,cb)=>cb(null,JSON.stringify(JSON.parse(c),null,2))}))" 

# Or just use jq like a normal person
cat file.json | jq .

# Find and replace in files
find . -name "*.js" -exec sed -i 's/oldtext/newtext/g' {} +

# Count lines of code (excluding node_modules)
find . -name '*.js' -o -name '*.ts' -o -name '*.tsx' | grep -v node_modules | xargs wc -l

# List all installed global packages
npm list -g --depth=0

# Check package size before installing
npx package-size axios lodash      # Compare sizes

# Open package in npm website
npm docs <package-name>

# Find unused dependencies
npx depcheck

# Update package.json with interactive UI
npx npm-check-updates -i
```

---

## 🆘 Emergency Commands (When Everything is on Fire)

```bash
# Full project reset (the "start over" button)
rm -rf node_modules .next dist build package-lock.json
npm cache clean --force
npm install

# Git undo last commit (keep changes)
git reset --soft HEAD~1

# Git nuke local changes
git checkout -- .
git clean -fd

# Restore deleted file from git
git checkout HEAD -- path/to/file

# Find what's using all the memory
top -o mem                         # Mac
htop                               # Linux (install first)

# When npm is completely broken
npm config set registry https://registry.npmjs.org/

# Reinstall npm itself (desperate times)
curl -qL https://www.npmjs.com/install.sh | sh
```

---

## 📝 Quick Reference Card

| What You Want | Command |
|---------------|---------|
| New project | `npm init -y` |
| Install package | `npm i <pkg>` |
| Dev dependency | `npm i -D <pkg>` |
| Run script | `npm run <script>` |
| Update all | `npm update` |
| Check outdated | `npm outdated` |
| Security audit | `npm audit fix` |
| Clear cache | `npm cache clean --force` |
| Kill port 3000 | `npx kill-port 3000` |
| Full reset | `rm -rf node_modules && npm i` |

---

## 🎬 The End Credits

Remember:
- `node_modules` is not a place for humans
- "Works on my machine" is not a deployment strategy
- If in doubt, `rm -rf node_modules && npm install`
- Coffee is a valid debugging tool
- Stack Overflow is your friend (but read the dates on answers)

**Now go build something awesome!** 🚀

---

*Made with ☕ and mass amounts of mass confusion by a developer who has typed `npm install` more times than they've said "I love you"*

*Last updated: Your current existential crisis*

