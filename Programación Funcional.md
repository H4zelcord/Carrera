Se asume que:
"Se puede meter un método o función dentro de una variable"
"Existen funciones que se pueden escribir en 1 línea"

```Java
number.forEach(n -> System.out.print(n + " "));

// O BIEN

number.forEach(System.out::print);
```

