# Verificação do Banco de Dados Supabase

## Como Verificar os Cadastros

### Opção 1: Via Dashboard do Supabase (Mais Fácil)

1. Acesse: https://app.supabase.com
2. Faça login com sua conta
3. Selecione o projeto "DELIVERY_IFOOD"
4. No menu à esquerda, clique em **"SQL Editor"**
5. Execute as queries abaixo para verificar os dados

### Opção 2: Via CLI do Supabase

```bash
# Instalar Supabase CLI (se não tiver)
npm install -g supabase

# Fazer login
supabase login

# Listar status do projeto
supabase status

# Acessar banco via psql
supabase db push  # Sincroniza migrações
```

## Queries para Verificar Cadastros

### 1. Ver todas as lojas
```sql
SELECT id, owner_id, name, slug, description, is_active, is_open, created_at 
FROM public.stores 
ORDER BY created_at DESC;
```

### 2. Ver todos os usuários
```sql
SELECT id, email, user_metadata, created_at 
FROM auth.users 
ORDER BY created_at DESC;
```

### 3. Ver roles dos usuários
```sql
SELECT ur.user_id, ur.role, u.email, u.user_metadata
FROM public.user_roles ur
JOIN auth.users u ON ur.user_id = u.id
ORDER BY ur.created_at DESC;
```

### 4. Ver perfis dos usuários
```sql
SELECT id, full_name, phone, avatar_url, is_active, created_at
FROM public.profiles
ORDER BY created_at DESC;
```

### 5. Ver produtos de uma loja
```sql
SELECT p.id, p.name, p.price, p.promo_price, p.section, p.is_available, s.name as store_name
FROM public.products p
JOIN public.stores s ON p.store_id = s.id
ORDER BY s.name, p.section, p.sort_order;
```

### 6. Ver pedidos
```sql
SELECT o.id, o.status, o.total, o.created_at, 
       s.name as store_name, c.full_name as customer_name
FROM public.orders o
JOIN public.stores s ON o.store_id = s.id
JOIN public.profiles c ON o.customer_id = c.id
ORDER BY o.created_at DESC
LIMIT 20;
```

### 7. Ver endereços cadastrados
```sql
SELECT a.id, a.label, a.street, a.number, a.city, a.state,
       p.full_name as user_name
FROM public.addresses a
JOIN public.profiles p ON a.user_id = p.id
ORDER BY a.created_at DESC;
```

### 8. Ver categorias de lojas
```sql
SELECT c.id, c.name, c.emoji, COUNT(sc.store_id) as total_stores
FROM public.categories c
LEFT JOIN public.store_categories sc ON c.id = sc.category_id
WHERE c.is_active = true
GROUP BY c.id, c.name, c.emoji
ORDER BY c.sort_order;
```

### 9. Ver horários das lojas
```sql
SELECT s.name, sh.weekday, sh.opens_at, sh.closes_at, sh.is_closed
FROM public.store_hours sh
JOIN public.stores s ON sh.store_id = s.id
ORDER BY s.name, sh.weekday;
```

### 10. Verificar permissões RLS (Row Level Security)
```sql
-- Ver políticas ativas
SELECT tablename, policyname, permissive, roles, qual, with_check
FROM pg_policies
WHERE schemaname = 'public'
ORDER BY tablename;
```

## Estrutura das Tabelas Principais

### Tabela: stores
```
- id (UUID): ID único da loja
- owner_id (UUID): ID do proprietário (refs auth.users)
- name (TEXT): Nome da loja
- slug (TEXT): URL-friendly name
- description (TEXT): Descrição
- logo_url (TEXT): URL da logo
- phone (TEXT): Telefone
- address fields (street, number, district, city, state, zip_code)
- delivery_fee (NUMERIC): Taxa de entrega
- min_order (NUMERIC): Pedido mínimo
- avg_prep_minutes (INT): Tempo médio de preparo
- service_radius_km (NUMERIC): Raio de entrega
- is_open (BOOLEAN): Se está aberta
- is_active (BOOLEAN): Se está ativa
- rating (NUMERIC): Avaliação média
- rating_count (INT): Quantidade de avaliações
```

### Tabela: products
```
- id (UUID): ID único do produto
- store_id (UUID): ID da loja
- name (TEXT): Nome do produto
- description (TEXT): Descrição
- price (NUMERIC): Preço
- promo_price (NUMERIC): Preço promocional
- image_url (TEXT): URL da imagem
- section (TEXT): Seção/categoria do produto
- is_available (BOOLEAN): Se está disponível
- sort_order (INT): Ordem de exibição
```

### Tabela: orders
```
- id (UUID): ID único do pedido
- store_id (UUID): ID da loja
- customer_id (UUID): ID do cliente
- status (order_status): Status do pedido
- total (NUMERIC): Valor total
- delivery_fee (NUMERIC): Taxa de entrega
- discount (NUMERIC): Desconto aplicado
- notes (TEXT): Observações
- created_at (TIMESTAMPTZ): Data/hora de criação
- updated_at (TIMESTAMPTZ): Última atualização
```

## Verificar se há permissões de upload

```sql
-- Ver buckets de storage
SELECT name, id, public 
FROM storage.buckets 
ORDER BY name;

-- Ver arquivos no bucket product_images
SELECT name, id, updated_at 
FROM storage.objects 
WHERE bucket_id = 'product_images'
LIMIT 10;
```

## Dicas Importantes

- As lojas criadas inicialmente (de teste) têm `owner_id = NULL`
- Ao criar uma loja, o campo `owner_id` deve ser preenchido com o UUID do usuário autenticado
- As políticas RLS garantem que cada usuário veja apenas seus próprios dados
- Os uploads de imagens vão para o bucket `product_images` no Storage do Supabase
- Os pedidos são criados apenas por usuários com role "cliente"
