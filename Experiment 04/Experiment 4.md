# Experiment 4

## Buffer overflow vulnerability using C

## Aim

To observe how an oversized copy or unbounded input can exceed a C buffer, affect adjacent data and produce undefined behaviour, then compare bounded input and copying techniques.

## Theory

A buffer is a fixed region of storage. A C string also needs a terminating `\0`, so `char name[10]` holds at most nine characters plus that terminator. Functions such as `strcpy` receive no destination capacity. An unbounded `%s` conversion similarly reads a token without knowing the array size.

Writing outside an array is undefined behaviour. A demonstration may corrupt nearby data, crash or appear to work. A particular output or overwrite is not guaranteed by C. Adjacent members of a structure make corruption easier to illustrate, but do not make an out-of-bounds write valid. `%p` and a `void *` cast can display addresses for comparison without assuming fixed addresses across runs.

The supplied PDF demonstrates neighbouring data corruption. Bounded operations prevent the overflow by respecting the destination size. Width-limited `scanf` reads a bounded token, `fgets` reads a bounded line, and `snprintf` writes formatted text within a specified size. Return values should be checked for failure or truncation. Compiler diagnostics, sanitizers and runtime protections complement correct bounds handling.

## Requirements and procedure

Use the course's online C compiler or an isolated local compiler. Save and compile each complete program separately because each defines `main`.

1. Run the normal copy and note the stored value and buffer capacity.
2. Compare the deliberately oversized copy and record compiler diagnostics.
3. Examine the structure example and its memory-address variant.
4. Compare an ordinary username with an oversized token in the input example.
5. Repeat with `%9s`, `fgets` and the bounded-copy snippet.

## C programs extracted from the PDF

The following transcriptions preserve source behaviour and sample input. Whitespace is normalised. Page numbers refer to PDF positions, which differ from some printed slide numbers. Oversized-copy examples are intentionally unsafe demonstrations.

### Normal buffer operation — PDF page 8

```c
#include <stdio.h>
#include <string.h>

int main()
{
    char name[10];
    strcpy(name, "Asif"); printf("Stored value: %s\n", name);
    printf("Buffer size: %zu bytes\n", sizeof(name));

return 0;
}
```

### Exceeding buffer capacity — PDF page 9

```c
#include <stdio.h>
#include <string.h>

int main()
{
    char name[10];
    strcpy(name, "ABCDEFGHIJKLMNOP");   // 16 chars into a 10-byte buffer
    printf("Value: %s\n", name);
    return 0;
}
```

### Visible memory corruption — PDF page 10

```c
#include <stdio.h>
#include <string.h>

struct StudentData {
    char buffer[8];
    char status[8];
};

int main()
{
    struct StudentData data;
    strcpy(data.status, "SAFE");

    printf("BEFORE: Status = %s\n", data.status);

    strcpy(data.buffer, "AAAAAAAAHACKED");  // oversized copy into buffer

    printf("AFTER:  Buffer = %s\n", data.buffer);
    printf("AFTER:  Status = %s\n", data.status);
    return 0;
}
```

### Inspecting member addresses — PDF page 12

```c
#include <stdio.h>
#include <string.h>

struct StudentData { char buffer[8]; char status[8]; };

int main()
{
    struct StudentData data;
    strcpy(data.status, "SAFE");

    printf("Address of buffer : %p\n", (void *)data.buffer);
    printf("Address of status : %p\n", (void *)data.status);

    strcpy(data.buffer, "AAAAAAAAHACKED");
    printf("After overflow, Status = %s\n", data.status);
    return 0;
}
```

### Unbounded user input — PDF page 13

```c
#include <stdio.h>

int main()
{
    char username[10];
    printf("Enter username: ");
    scanf("%s", username);
    printf("Username: %s\n", username);
    return 0;
}
```

### Width-limited scanf — PDF page 15

```c
#include <stdio.h>

int main()
{
    char username[10];
    printf("Enter username: ");
    scanf("%9s", username);      // restricted to 9 chars + '\0'
    printf("Username: %s\n", username);
    return 0;
}
```

### Bounded line input with fgets — PDF page 16

```c
#include <stdio.h>

int main()
{
    char username[10];
    printf("Enter username: ");
    fgets(username, sizeof(username), stdin);
    printf("Username: %s", username);
    return 0;
}
```

### Bounded copying — PDF page 17

This is a snippet to place inside a function with `<stdio.h>` included.

```c
char buffer[10];
snprintf(buffer, sizeof(buffer), "%s", "ABCDEFGHIJKLMNOPQRSTUVWXYZ");
printf("Stored value: %s\n", buffer);
```

### Local compilation example

This command is an added convenience, rather than text extracted from the PDF. Replace `example.c` with the chosen program file.

```shell
gcc -std=c11 -Wall -Wextra example.c -o example
```

## Observations and result

| Demonstration | Observation to record |
| --- | --- |
| Normal copy | `Asif` fits and the array has 10 bytes |
| Oversized copy | Warning, corruption or termination as observed |
| Structure example | Whether `status` changes from `SAFE` |
| Address example | Relationship between member addresses |
| `%9s` | At most nine characters stored, followed by a terminator |
| `fgets` | Bounded line input, with a newline retained if it fits |
| `snprintf` | `ABCDEFGHI` fits in the ten-byte destination |

The exercise demonstrates why bounds must be enforced at every write. Record actual unsafe-program behaviour without treating one observed output as guaranteed.

## Conclusion

The C examples illustrate that writing beyond an array's capacity causes undefined behaviour and can affect neighbouring data. Bounded input and copying, space for the string terminator, and checks for failure or truncation are essential to reliable buffer handling. Compiler warnings and runtime protections support these practices but do not replace correct bounds checking.

## Viva questions

- Why does a ten-byte C array normally hold only nine string characters?
- Why does successful compilation not prove that a copy is safe?
- How does `fgets` differ from a width-limited `%s` conversion?

## Sources

`Buffer_overflow.pdf`, PDF pages 3–17. Existing practical evidence is in [Experiment4.pdf](Experiment4.pdf).
