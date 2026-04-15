<p align="end">
   <strong>🌐 Cambiar idioma:</strong><br>
   <a href="/README.es.md">
    <img src="https://github.com/Nachopuerto95/multilang/blob/main/ES.png" alt="Español" width="50">
  </a>&nbsp;&nbsp;&nbsp;
  <a href="/README.md">
    <img src="https://github.com/Nachopuerto95/multilang/blob/main/EN.png" alt="English" width="50">
  </a>
</p>

# Pipex (42cursus)

<img src="https://github.com/Nachopuerto95/multilang/blob/main/42-Madrid%20-%20Edited.jpg">

## 📜 Sobre el proyecto

> Recrear el comportamiento de una pipeline de shell: `< infile cmd1 | cmd2 > outfile`.

```html
	🚀 Un proyecto práctico para entender de verdad cómo funciona la creación
	de procesos en UNIX, los descriptores de archivo y la comunicación entre
	procesos. Vas a tocar fork, execve, pipe y dup2 hasta que te salgan solos.
```

> [!NOTE]
> Por las normas de la escuela 42:
> * Ninguna función puede tener más de 25 líneas de código.
> * Todas las variables se declaran y alinean al principio de la función.
> * Solo se pueden usar las funciones permitidas por el enunciado.

### 📌 Descripción

Pipex lee de un archivo de entrada, pasa el contenido por un primer comando, redirige la salida al segundo comando a través de una pipe, y escribe el resultado en un archivo de salida. Es decir, exactamente esto que ya usas en terminal sin pensar:

```bash
< infile cmd1 | cmd2 > outfile
```

La gracia del proyecto es construir esa pipeline desde cero sin que el shell te lo resuelva por debajo. El repo implementa la parte obligatoria en C.

### 🚀 Lo que he trabajado

- Parsear `argv` para localizar el archivo de entrada, los dos comandos y el archivo de salida.
- Resolver cada comando contra la variable `PATH` del entorno.
- Crear una pipe y hacer `fork` para lanzar un proceso por comando.
- Redirigir `stdin`/`stdout` de cada hijo con `dup2` para que lean y escriban donde toca.
- Manejar errores: ficheros que no existen, comandos no encontrados, PATH inválido.

### 🔧 Cómo usarlo

Compilar:

```bash
make
```

Ejecutar:

```bash
./pipex infile "cmd1" "cmd2" outfile
```

Ejemplo — equivalente a `< file.txt grep hello | wc -l > out.txt`:

```bash
./pipex file.txt "grep hello" "wc -l" out.txt
```

### 🧠 Cómo funciona por dentro

```
           ┌──────────┐     pipe     ┌──────────┐
 infile ──►│  hijo 1  │──────────────►│  hijo 2  │──► outfile
           │  cmd1    │   fd[1]→fd[0]│  cmd2    │
           └──────────┘               └──────────┘
```

1. `main` abre `infile` en lectura y `outfile` en escritura (lo crea/trunca).
2. `pipe()` monta el canal de comunicación entre ambos procesos.
3. El primer hijo redirige su `stdin` al `infile` y su `stdout` al extremo de escritura de la pipe, luego hace `execve` del primer comando.
4. El padre redirige su `stdin` al extremo de lectura de la pipe y su `stdout` al `outfile`, y ejecuta el segundo comando.
5. `waitpid` espera al primer hijo antes de salir.

### 📂 Estructura

```
pipex/
├── pipex.c         # main y lógica fork/exec
├── pipex_utils.c   # resolución de PATH, errores
├── pipex.h
├── libft/          # helpers necesarios (split, strjoin, strdup…)
└── Makefile
```

### ⚠️ Alcance

Este repo implementa solo la parte **obligatoria**. Acepta exactamente dos comandos: no hay soporte de pipes encadenadas (`cmd1 | cmd2 | cmd3 | …`) ni del bonus de `here_doc`.
