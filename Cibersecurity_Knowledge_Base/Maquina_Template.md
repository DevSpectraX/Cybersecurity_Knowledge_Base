---
tags:
  - laboratorio
  - htb
  - dockerlabs
  - linux
  - windows
  - facil
  - medio
  - dificil
---

# 🎯 Máquina: {{title}}

| Campo      | Valor           |
| ---------- | --------------- |
| Plataforma | dockerlabs      |
| SO         | Linux / Windows |
| Dificultad |                 |
| IP         | `10.10.X.X`     |

---

# 📝 1. Resumen

## 1.1 Objetivo

## 1.2 Vector de entrada

## 1.3 Escalada de privilegios

---

# 🔍 2. Reconocimiento

## 2.1 Puertos

```bash
nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 10.10.X.X -oG allPorts
```

## 2.2 Servicios

```bash
nmap -sCV -p[PUERTOS] 10.10.X.X -oN targeted
```

## 2.3 Enumeración Web

```bash
whatweb http://10.10.X.X
gobuster dir -u http://10.10.X.X/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 20
```

---

# 🚀 3. Explotación

## 3.1 Vector de ataque

## 3.2 Acceso inicial

```bash
nc -nlvp 443
```

```bash
[payload]
```

Usuario:

```id="u3n8q5"
-
```

## 3.3 TTY

```bash
script /dev/null -c bash
Ctrl + Z
stty raw -echo; fg
reset xterm
export TERM=xterm
```

---

# 👑 4. Escalada de privilegios

## 4.1 Enumeración

```bash
sudo -l
find / -perm -4000 2>/dev/null
```

## 4.2 Explotación

## 4.3 Usuario final

```text
root / SYSTEM
```

---

# 🏴 5. Flags

## 5.1 User

```text
-
```

## 5.2 Root

```text
-
```

---

# 📚 6. Notas

## 6.1 Vector inicial

## 6.2 Escalada

## 6.3 Lecciones aprendidas