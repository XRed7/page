# Ataques de Fuerza Bruta en Local con John The Ripper

### 1. Crackear un archivo ZIP con John the Ripper

#### Pasos:

1.  **Crear un archivo de texto**:

    ```bash
    $ echo "yoooorm hashrm hash" > documento.txt
    ```
2.  **Verificar que el archivo se ha creado**:

    ```bash
    $ ls
    documento.txt
    ```
3.  **Comprimir el archivo en un ZIP con contraseña**:

    ```bash
    $ zip -e comprimido.zip documento.txt
    Enter password: P@ssw0rd!
    Verify password: P@ssw0rd!
    adding: documento.txt (deflated 25%)
    ```
4.  **Extraer el hash del archivo ZIP**:

    ```bash
    $ zip2john comprimido.zip > hash
    ```
5.  **Crackear la contraseña usando John the Ripper**:

    ```bash
    $ john --wordlist=/usr/share/wordlists/rockyou.txt hash
    Using default input encoding: UTF-8
    Loaded 1 password hash (PKZIP [32/64])
    Will run 4 OpenMP threads
    Press 'q' or Ctrl-C to abort, almost any other key for status
    P@ssw0rd!        (comprimido.zip/documento.txt)
    ```
6.  **Descomprimir el archivo ZIP** usando la contraseña encontrada:

    ```bash
    $ unzip comprimido.zip 
    Archive:  comprimido.zip
    [comprimido.zip] documento.txt password: P@ssw0rd!
    ```

***

### 2. Crackear un archivo KeePass 2x

#### Pasos:

1.  **Descargar la base de datos de KeePass**:

    ```bash
    $ wget http://192.168.1.15/Database.kdbx  
    --2024-10-13 03:18:11--  http://192.168.1.15/Database.kdbx
    Connecting to 192.168.1.15:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 1902 (1.9K) [application/octet-stream]
    Saving to: ‘Database.kdbx’

    Database.kdbx                           100%[=============================================================================>]   1.86K  --.-KB/s    in 0s      
    ```
2.  **Extraer el hash de la base de datos**:

    ```bash
    $ keepass2john Database.kdbx > hash
    ```
3.  **Crackear la contraseña usando John the Ripper**:

    ```bash
    $ john --wordlist=/usr/share/wordlists/rockyou.txt hash
    Using default input encoding: UTF-8
    Loaded 1 password hash (KeePass [SHA256 AES 32/64])
    Passw0rd         (Database)     
    ```

***

### 3. Crackear hashes con John the Ripper

#### Pasos:

1.  **Crear un archivo con el hash a crackear**:

    ```bash
    $ echo "d41e98d1eafa6d6011d3a70f1a5b92f0" > hash
    ```
2.  **Identificar el tipo de hash**:

    ```bash
    $ hash-identifier
    /usr/share/hash-identifier/hash-id.py:13: SyntaxWarning: invalid escape sequence '\ '
    HASH: d41e98d1eafa6d6011d3a70f1a5b92f0

    Possible Hashes:
    [+] MD5
    ```
3. **Crackear el hash usando John the Ripper**:

```bash
$ john --wordlist=/usr/share/wordlists/rockyou.txt hash
   Warning: detected hash type "LM", but the string is also recognized as "dynamic=md5($p)"
   Passw0rd         (?)
```

4. **Si es necesario, crackear el hash como Raw-MD5**:

<pre class="language-bash"><code class="lang-bash"><strong>$ john --wordlist=/usr/share/wordlists/rockyou.txt --format=Raw-MD5 hash Loaded 1 password hash (Raw-MD5 [MD5 128/128 SSE2 4x3]) Passw0rd (?)
</strong></code></pre>
