# Guia de Uso - Sistema de Loja/Restaurante

## 🚀 Fluxo Completo para Restaurantes

### 1️⃣ Cadastro e Login

1. Acesse `http://localhost:5173/auth`
2. Clique em "Criar conta"
3. Preencha:
   - **Email**: seu@email.com
   - **Senha**: uma senha segura
   - **Nome completo**: Nome da sua loja
   - **Telefone**: seu telefone
   - **Tipo de conta**: Selecione **"loja"**
4. Confirme o email (verifique sua caixa de entrada)
5. Você será redirecionado para a página inicial

### 2️⃣ Criar/Editar Sua Loja

1. Acesse `http://localhost:5173/painel-loja`
2. Você verá a mensagem: "Nenhuma loja vinculada"
3. Clique em **"Criar Loja"**
4. Preencha os dados:
   - **Logo da Loja** (opcional): faça upload de uma imagem
   - **Nome**: ex: "Pizzaria da Sofia"
   - **Descrição**: "Pizzas artesanais na lenha"
   - **Telefone**: (11) 99999-9999
   - **Endereço**: rua, número, bairro, cidade, estado, CEP
   - **Taxa de Entrega**: R$ 5,00
   - **Pedido Mínimo**: R$ 20,00
   - **Tempo de Preparo**: 35 minutos
   - **Raio de Entrega**: 8 km
5. Clique em **"Criar Loja"**
6. Você será redirecionado ao painel

**Para editar sua loja depois:**
- Clique no botão **"Editar"** no canto superior direito do painel

### 3️⃣ Adicionar Produtos

1. No painel da loja, clique em **"Produtos"**
2. Clique em **"Novo Produto"**
3. Preencha:
   - **Imagem**: faça upload da foto do produto
   - **Nome**: ex: "Pizza Margherita"
   - **Descrição**: "Molho, muçarela e manjericão"
   - **Preço**: R$ 52,00
   - **Preço Promocional** (opcional): R$ 45,00
   - **Seção**: ex: "Pizzas Salgadas"
   - **Disponível**: ative se está disponível
4. Clique em **"Criar"**

**Gerenciar Produtos:**
- Ver todos: acesse `/produtos`
- Editar: clique no ícone de edição
- Deletar: clique no ícone de lixeira
- Filtrar por seção: produtos são agrupados automaticamente

### 4️⃣ Gerenciar Pedidos

1. No **Painel da Loja** (`/painel-loja`), você verá todos os pedidos
2. Para cada pedido, você pode:
   - **Avançar status**: clique no botão "Avançar" para mover para o próximo status
   - **Cancelar**: clique em "Cancelar" se necessário
3. Estados de um pedido:
   - 🟡 **PENDING** (Pendente) → Aceitar pedido
   - 🟠 **ACCEPTED** (Aceito) → Começar preparo
   - 🟡 **PREPARING** (Preparando)
   - 🟢 **READY_FOR_PICKUP** (Pronto para coleta)
   - 🔵 **OUT_FOR_DELIVERY** (Em entrega)
   - ✅ **DELIVERED** (Entregue) - Final
   - ❌ **CANCELLED** (Cancelado) - Final

**Abrir/Fechar Loja:**
- Use o switch **"Loja aberta"** no painel para aparecer ou não no app

### 📊 Verificar Dados do Banco de Dados

#### Via Dashboard do Supabase:
1. Acesse: https://app.supabase.com
2. Selecione o projeto
3. Vá em **"SQL Editor"**
4. Execute as queries do arquivo `VERIFICAR_BANCO_DE_DADOS.md`

#### Via CLI:
```bash
# Verificar status
npm run db:verify

# Ou manualmente com psql:
supabase db pull  # Sincroniza schema
```

### 🔍 Verificar Cadastros Específicos

```bash
# Verificar tudo de uma vez
npm run db:verify

# Isso vai mostrar:
# - Lojas cadastradas
# - Usuários
# - Roles (perfil de cada usuário)
# - Produtos
# - Pedidos recentes
```

### 📁 Estrutura de URLs

| Página | URL | Descrição |
|--------|-----|-----------|
| **Login/Cadastro** | `/auth` | Fazer login ou criar conta |
| **Painel da Loja** | `/painel-loja` | Dashboard com pedidos |
| **Editar Loja** | `/editar-loja` | Alterar dados da loja |
| **Gerenciar Produtos** | `/produtos` | Adicionar/editar/deletar produtos |
| **Meus Pedidos (Cliente)** | `/pedidos` | Ver histórico de pedidos |
| **Detalhes do Pedido** | `/pedidos/:id` | Ver detalhes de um pedido |

### 🔐 Segurança

- Cada usuário só pode:
  - Ver suas próprias lojas
  - Gerenciar seus próprios produtos
  - Ver seus próprios pedidos
- Políticas RLS (Row Level Security) garantem isso no banco de dados
- As imagens são armazenadas em um bucket privado do Supabase

### 💾 Upload de Imagens

- **Tamanho máximo**: 5MB
- **Formatos aceitos**: JPG, PNG, GIF, WebP
- **Local de armazenamento**: Supabase Storage → bucket "product_images"

### 🎯 Próximos Passos Recomendados

1. ✅ Criar uma conta como restaurante
2. ✅ Cadastrar sua loja com informações básicas
3. ✅ Adicionar pelo menos 5 produtos com fotos
4. ✅ Testar recebimento de pedidos
5. ⏳ Implementar: avaliações de clientes
6. ⏳ Implementar: cupons e promoções
7. ⏳ Implementar: histórico de faturamento

### 🆘 Troubleshooting

**Problema**: "Nenhuma loja vinculada" ao entrar
**Solução**: Clique em "Criar Loja" para criar uma nova loja

**Problema**: Upload de imagem falha
**Solução**: Verifique o tamanho (máx 5MB) e formato (JPG, PNG, GIF, WebP)

**Problema**: Pedidos não aparecem no painel
**Solução**: Verifique se a loja está aberta (switch "Loja aberta")

**Problema**: Não consegue fazer login
**Solução**: Confirme seu email na caixa de entrada ou use "Recuperar senha"

---

📚 **Documentação Adicional**: Ver `VERIFICAR_BANCO_DE_DADOS.md` para queries SQL
