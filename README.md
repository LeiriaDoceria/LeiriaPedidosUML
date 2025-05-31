## Diagrama de Componentes

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