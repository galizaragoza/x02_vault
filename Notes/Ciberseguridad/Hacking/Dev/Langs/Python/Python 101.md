# Python

## Data types
```python
int = 10
float = 2.5
str = "Python"
bool = true

list = ['Bash', 'Golang', 'Python']
tuple = (1.5, 2.5) ## Los tuples son listas con valores inmutables
set = {1, 2, 4, 5} ## Los sets son listas en los que cada valor debe ser único
dict = {'lang': 'Python', 'OOP': 'Yes'} ## Key = val
```

## Type annotations
```python
lang: str = 'Python' ## Similar a TS, se puede definir el tipo de la variable (opcional)
```

## Constantes
En Python no hay constantes, pero se puede usar Final para indicar que un valor debería mantenerse fijo, el editor dará un aviso si tratamos de redefinirlo
```python
from typing import Final

VERSION: Final[str] = '3'
VERSION = '4' ## Esto daría un error
```

## Funciones
```python
import os

def greet() -> None:
	print('Buenos días!')
```

```python
import os

def greetV2(name: str) -> None:
	print('¿Quien eres?')
	name = input()
	print(f'Buenos días, {name}!')
```

```python
def add(a: int, b: int) -> float:
	return a + b
	

print(add(1,2))
```

## Clases
```python
class Lang:
	def __init__(self, oop: str, colour: str) -> None:
		self.oop = oop
		self.colour = color
python: Lang = Lang('yes', 'yellow')
print(python.colour)
```

```python
class Lang:
	def __init__(self, oop: str, colour: str) -> None:
		self.oop = oop
		self.colour = color
	
	def show(self) -> None:
		print (f'{self.oop}, it is OOP')
		
	def allInfo(self) -> None:
		print (f'Lang is {self.oop} and {self.colour}')
```

## Dunder methods


# Recursos / fuentes
[10 Important Python Concepts In 20 Minutes](https://youtu.be/Gx5qb1uHss4?list=PL65_wYSEg5HdSq1pSZxQmz7SoNE48vOgR)
[Dunder methods](https://www.geeksforgeeks.org/python/dunder-magic-methods-python/)
