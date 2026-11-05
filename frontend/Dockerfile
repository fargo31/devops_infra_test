# --- Étape 1 : Installation des dépendances ---
FROM node:22-alpine AS deps
RUN apk add --no-cache libc6-compat
WORKDIR /app

COPY package*.json ./
RUN npm ci

# --- Étape 2 : Build de l'application Next.js ---
FROM node:22-alpine AS builder
WORKDIR /app

COPY --from=deps /app/node_modules ./node_modules
COPY . .

# Désactiver la télémétrie Next.js pendant le build
ENV NEXT_TELEMETRY_DISABLED=1

RUN npm run build

# --- Étape 3 : Image de Production finale (Ultra-légère) ---
FROM node:22-alpine AS runner
WORKDIR /app

ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1

# Sécurité DevOps : Utilisateur non-root
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

# Récupération sélective des fichiers générés en mode standalone
COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT=3000
ENV HOSTNAME="0.0.0.0"

# Exécution du serveur autonome Node.js généré par Next.js
CMD ["node", "server.js"]
