# Setup

Stand: 2026-10-05

**Was der Ordner ist:** Ein Fork von Conway-Research/automaton (v0.2.1), die Runtime für einen autonomen KI-Agenten mit eigenem Krypto-Wallet. Er läuft in einer Dauerschleife, führt Shell-Befehle aus, schreibt Dateien, kann seinen eigenen Code ändern und Kind-Agenten starten. Er bezahlt seine Rechenleistung selbst (Conway Credits, gekauft mit USDC auf Base) und stoppt, wenn das Geld aus ist.

**Stand auf dem Rechner:** Node 22.19 und pnpm 10.28.1 passen. Installiert, gebaut und eingerichtet ist noch nichts.

## Was er von dir braucht

Der Setup-Wizard fragt beim ersten Start:

1. **Chain:** `evm` (Standard) oder `solana`.
2. **Name** des Agenten.
3. **Genesis-Prompt:** der Auftrag des Agenten und die wichtigste Eingabe. Vage Prompts führen laut Doku zu ziellosem Verhalten.
4. **Deine eigene Wallet-Adresse** (Pflichtfeld, `0x...`). Du brauchst also vorher ein eigenes Wallet.
5. **Optional:** OpenAI-Key, Anthropic-Key oder Ollama-URL.
6. **Ausgabenlimits:** Enter übernimmt die Standardwerte.
7. **Geld:** mindestens 5 $ USDC auf Base an die Agent-Adresse, die der Wizard anzeigt.

Den Conway-API-Key holt er sich selbst per Wallet-Signatur, ein Account ist nicht nötig. Ob das aktuell klappt, ist nicht getestet.

## Was du vorher wissen musst

- **Keine Sandbox auf deinem Rechner:** Lokal führt der Agent Befehle direkt auf deinem PC mit deinen Rechten aus ([client.ts:111](src/conway/client.ts#L111)). Die Schutzliste gegen gefährliche Befehle kennt nur Unix-Befehle wie `rm` und `cat` und greift unter Windows nicht ([command-safety.ts:37](src/agent/policy-rules/command-safety.ts#L37)).
- **Windows ist nicht vorgesehen:** Der Code erwartet die Variable `HOME`, die in PowerShell leer ist. Der Private Key würde dadurch voraussichtlich unter `C:\root\.automaton` landen, und der Dateischutz (0600) wirkt unter Windows nicht.
- **Eigene Keys ersetzen das Guthaben nicht:** Im Survival-Code gibt es keine Ausnahme für eigene Keys oder Ollama. Bei null Conway Credits geht er nach der Gnadenfrist auf „dead“ ([tasks.ts:110](src/heartbeat/tasks.ts#L110)).
- **Die Standardlimits sind hoch:** 500 $ pro Tag für Inferenz und 250 $ pro Tag für Transfers. Die echte Obergrenze ist, was im Wallet liegt, also nur einen kleinen Betrag einzahlen.
- **Der Dienst ist nur teilweise erreichbar:** `api.conway.tech` und `app.conway.tech` antworten, `social.conway.tech` löst nicht auf. Die Agent-zu-Agent-Nachrichten werden also nicht funktionieren.
- **Die Doku ist teils veraltet:** Sie nennt Version 0.1.0 und widersprüchliche Tool-Zahlen (57 und 69). Das README sagt selbst, dass die Entwicklung intern weiterläuft.

## Start

```bash
pnpm install
pnpm build
node dist/index.js --run
```

Empfehlung: nicht direkt unter Windows starten, sondern mindestens im vorhandenen WSL-Ubuntu. Auch das ist keine echte Isolation, weil WSL auf `C:` zugreifen kann. Sauber getrennt wäre nur eine Conway-Sandbox oder eine eigene VM.

## Offene Entscheidungen

- **Ziel:** Nur verstehen und ausprobieren, oder soll der Agent wirklich etwas Bestimmtes verdienen? Davon hängen Genesis-Prompt und Budget ab.
- **Wallet:** Gibt es schon ein Wallet mit USDC auf Base?
- **Ort:** WSL, VM oder Conway-Sandbox?
