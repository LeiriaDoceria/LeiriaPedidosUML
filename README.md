## Diagrama de Classes

![image](https://github.com/user-attachments/assets/9823fe70-edfe-4f12-96ea-e0eaf4c14c64)


- `Pedido` – Representa um pedido feito por um cliente.
- `Produto` – Representa um item vendável.
- `ItemPedido` – Representa a relação entre pedidos e produtos, contendo também a quantidade.

### Pedido

| Campo              | Tipo     | Descrição                              |
|--------------------|----------|----------------------------------------|
| `nome_cliente`     | string   | Nome completo do cliente.              |
| `numero_cliente`   | string   | Telefone ou número de contato.         |
| `endereco_cliente` | string   | Endereço de onde o cliente é.          |
| `endereco_entrega` | string   | Local da entrega.                      |
| `valor`            | string   | Valor total do pedido.                 |
| `data_entrega`     | date     | Data programada da entrega.            |
| `hora_entrega`     | string   | Hora da entrega.                       |
| `observacoes`      | string   | Notas adicionais do pedido.            |

### Produto

| Campo           | Tipo     | Descrição                                 |
|------------------|----------|---------------------------------------------|
| `nome`           | string   | Nome do produto.                           |
| `medido_por`     | string   | Unidade de medida (kg, unidade, etc).      |
| `preco`          | double   | Preço unitário.                            |
| `observacoes`    | string   | Observações adicionais sobre o produto.    |

### ItemPedido

| Campo         | Tipo     | Descrição                                    |
|---------------|----------|----------------------------------------------|
| `pedido`      | Pedido   | Referência ao pedido relacionado.            |
| `produto`     | Produto  | Produto relacionado ao item.                 |
| `quantidade`  | string   | Quantidade solicitada do produto.            |

## Relacionamentos

- Um `Pedido` pode conter vários `ItemPedido`.
- Um `Produto` pode aparecer em vários `ItemPedido`.
- Cada `ItemPedido` está vinculado a **um** `Pedido` e **um** `Produto`.

## Implementação em código

```python
from django.db import models

# Create your models here.
class Produto(models.Model):
    MEDIDO_POR_CHOICES = [
        ('UNIDADE', 'Unidade'),
        ('CENTO', 'Cento'),
        ('QUILO', 'Quilo')
    ]

    nome = models.CharField(max_length=50)
    medido_por = models.CharField(
        max_length=10,
        choices=MEDIDO_POR_CHOICES,
        default='UNIDADE'
    )
    preco = models.DecimalField(max_digits=6, decimal_places=2)
    observacoes = models.TextField(max_length=75, blank=True, null=True)

    def __str__(self):
        return(f"{self.nome}")
    
class Pedido(models.Model):
    nome_cliente = models.CharField(max_length=50)
    numero_cliente = models.CharField(max_length=20)
    endereco_cliente = models.TextField(max_length=100)
    endereco_entrega = models.TextField(max_length=100)
    produtos = models.ManyToManyField(Produto, through="ItemPedido")
    valor = models.DecimalField(default=0.00, max_digits=6, decimal_places=2)
    data_entrega = models.DateField(null=True)
    hora_entrega = models.TimeField(null=True)
    observacoes = models.TextField(max_length=100, blank=True, null=True)

    def __str__(self):
        return(f"Pedido #{self.id}")
    
class ItemPedido(models.Model):
    pedido = models.ForeignKey(Pedido, on_delete=models.CASCADE)
    produto = models.ForeignKey(Produto, on_delete=models.CASCADE)
    quantidade = models.PositiveIntegerField()
```

## Diagrama de Componentes

![image](https://github.com/user-attachments/assets/5ba00d24-5f50-449c-9baf-d75d38ff6b92)

### Client Browser
- Interface do usuário (ex: navegador).
- Envia requisições HTTP (GET, POST, PUT, DELETE).

### URLs
- Define o roteamento das URLs para as views apropriadas.
- Arquivo: `urls.py`

```python
urlpatterns = [
    path('', views.home, name='homepage'),
    path('sair/', views.logout_user, name='user-logout'),
    path('produto/<int:pk>', views.produto_item, name='produto'),
    path('pedido/<int:pk>', views.pedido_read, name='pedido'),
    ...
]
```

### Views
Controla o fluxo principal de dados mas também é o responsável por receber e tratar requisições, buscar dados nos models, processar forms e renderizar templates.

Arquivo: `views.py`

### Models

Define as estruturas de dados (tabelas no banco de dados).

Models:
- Pedido (nome_cliente, produtos, valor...)
- Produto (nome, medido_por, preco... )
- ItemPedido (pedido, produto, quantidade...)

Arquivo: `models.py`

```python
class Pedido(models.Model):
    nome_cliente = models.CharField(max_length=50)
    produtos = models.ManyToManyField(Produto, through="ItemPedido")
    valor = models.DecimalField(default=0.00, max_digits=6, decimal_places=2)
    ...

    def __str__(self):
        return(f"Pedido #{self.id}")
]
```

### Forms

Define formulários baseados nos models e valida os dados do usuário antes de salvar.

Arquivo: `forms.py`

```python
class PedidoForm_1(ModelForm):
    class Meta:
        model = Pedido
        fields = ('nome_cliente', 'numero_cliente', ...)
        labels = {
            'nome_cliente': 'Nome do Cliente',
            'endereco_cliente': 'Endereço do Cliente',
            ...
        }
        widgets = {
            'nome_cliente': forms.TextInput(attrs={'class': 'form-control'}),
            'endereco_entrega': forms.TextInput(attrs={'class': 'form-control'}),
            ...
        }

```


### Templates

Arquivos HTML com marcações Django. Ex: ```{% for produto in produtos %}``` \
Recebem dados das views para renderização dinâmica.

Pasta: `templates/`

### Static

Contém arquivos como CSS, JS e imagens. \
Utilizados pelos templates para estilização e interatividade.

Pasta: `static/`

### Admin

Sistema automático de administração do Django. \
Permite gerenciar produtos e pedidos via interface web.

```python
admin.site.register(Produto)
admin.site.register(Pedido)
admin.site.register(ItemPedido)
```

### Middleware

Intercepta todas as requisições e respostas.

Pode ser usado para:
- Autenticação
- Log de acesso
- Validações globais
- Controle de tempo de execução

É configurado no `settings.py` via a lista `MIDDLEWARE`.

### Database

Armazena as informações persistentes.\
Manipulado apenas via Models.

### Responses
Saídas do Django para o cliente via:

- `HTML (via HttpResponse)` 
- `Redirecionamento (HttpResponseRedirect)`
