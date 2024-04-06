# Tutorial dados em Cache utilizando Flask

## Tutoriais utilizados
[Codemann](https://codedamn.com/news/backend/rest-api-caching-advanced-techniques)

[StackOverFlow](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/)

[LogRocket](https://blog.logrocket.com/caching-strategies-to-speed-up-your-api/)

<hr>

### Passo 1
Instalar a biblioteca `flask` e a biblioteca `cache` do `flask`.
```
pip install flask
pip install flask-caching
```

<hr>


### Passo 2
Importar dentro do arquivo pyhton as bibliotecas previamente instaladas.
```
from flask import Flask, jsonify
from flask_caching import Cache
```

<hr>


### Passo 3
Inicar sua aplicação `Flask`.
```
app = Flask(_name_)
```

<hr>


### Passo 4
Definir qual tipo de cache vamos utilizar e o tempo que os dados ficarão armazenados.
```
cache = Cache(app, config={'CACHE_TYPE': 'SimpleCache', 'CACHE_DEFAULT_TIMEOUT': 60})
```

<hr>


### Passo 5
Aqui utilizamos de uma pequena implementação do calculo de um numero fatorial (para fins de demonstração).
```
# Função para calcular o fatorial de um número
def calcular_fatorial(n):
    if n == 0:
        return 1
    else:
        return n * calcular_fatorial(n - 1)
    
```

<hr>


### Passo 6
Criar uma rota utilizando `Flask` que chama a função calcular_fatorial() e recebe na rota o valor que será calculado.
```
@app.route('/fatorial/<int:valor>')
```

<hr>


### Passo 7
Aqui criamos uma função para verificar se os dados que estão sendo requisitados pela rota já estão armazenados no cache.
Caso não estejam armazenados o resultado é calculado e armazenado no cache.
```
def calcular_e_armazenar_fatorial(valor):
    
    # Verifica se os dados estão no cache
    fatorial_data = cache.get(f'/fatorial/{valor}')
    if fatorial_data is not None:
        return jsonify({'num': valor, 'fatorial': fatorial_data, 'cache': True})

    # Se não estiver em cache, calcula o fatorial
    resultado_fatorial = calcular_fatorial(valor)
    cache.set(f'/fatorial/{valor}', resultado_fatorial)

    return jsonify({'num': valor, 'fatorial': resultado_fatorial, 'cache': False})
```
<br>
Aqui o app foi rodado pela primeira vez, e como podemos ver, como esta rota nunca tinha sido requisitada não possuia nenhum valor guardado em cache sendo indicado pelo json retornado com `"cache": false`.

![Imagem do WhatsApp de 2024-04-05 à(s) 21 08 42_a24d4827](https://github.com/joaolucascalorio/Trabalho-de-infraestrutura/assets/49102217/46fb4717-5efc-467f-beb3-3d7e52fa9c3f)

**_Imagem 1: Aplicação rodando sem dados no cache._**

<br>
Aqui o app foi rodado pela segunda vez, e como podemos ver, como esta rota já tinha sido requisitada, possuia um valor guardado em cache sendo indicado pelo json retornado com `"cache": true`.

![Imagem do WhatsApp de 2024-04-05 à(s) 21 09 37_e31c6c06](https://github.com/joaolucascalorio/Trabalho-de-infraestrutura/assets/49102217/98da3e2e-4786-4f77-96e4-10a4090e2590)

**_Imagem 2: Aplicação rodando com dados no cache._**

<hr>

### Passo 8
Por fim para que o servidor web flask seja rodado é preciso dar um `app.run`.
```
if _name_ == '_main_':
    app.run(debug=True)
```

<hr>

### Código completo da aplicação
```
from flask import Flask, jsonify
from flask_caching import Cache

app = Flask(_name_)
cache = Cache(app, config={'CACHE_TYPE': 'SimpleCache', 'CACHE_DEFAULT_TIMEOUT': 60})

# Função para calcular o fatorial de um número
def calcular_fatorial(n):
    if n == 0:
        return 1
    else:
        return n * calcular_fatorial(n - 1)
    

@app.route('/fatorial/<int:valor>')
def calcular_e_armazenar_fatorial(valor):
    
    # Verifica se os dados estão no cache
    fatorial_data = cache.get(f'/fatorial/{valor}')
    if fatorial_data is not None:
        return jsonify({'num': valor, 'fatorial': fatorial_data, 'cache': True})

    # Se não estiver em cache, calcula o fatorial
    resultado_fatorial = calcular_fatorial(valor)
    cache.set(f'/fatorial/{valor}', resultado_fatorial)

    return jsonify({'num': valor, 'fatorial': resultado_fatorial, 'cache': False})

if _name_ == '_main_':
    app.run(debug=True)
```
