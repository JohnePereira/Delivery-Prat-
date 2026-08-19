# 🪣 Como Criar o Bucket no Supabase

O bucket `product-images` não foi criado. Escolha um dos métodos abaixo:

---

## 📌 **Método 1: Manual via Dashboard (Mais Fácil)**

### Passo 1: Acesse o Dashboard
1. Abra: https://app.supabase.com
2. Faça login
3. Selecione seu projeto **DELIVERY_IFOOD**

### Passo 2: Vá em Storage
1. No menu lateral, clique em **Storage**
2. Clique em **Create new bucket**

### Passo 3: Criar o Bucket
- **Name**: `product-images`
- **Public bucket**: ✅ SIM (marque)
- Clique em **Create bucket**

### Passo 4: Executar Políticas SQL
1. Vá em **SQL Editor** (menu lateral)
2. Cole o conteúdo do arquivo: `CREATE_BUCKET_MANUALLY.sql`
3. Clique em **Run**

**Pronto!** O bucket está criado e as políticas também! ✓

---

## 🔧 **Método 2: Automático via Node.js**

### Passo 1: Obter SERVICE_ROLE_KEY
1. Acesse: https://app.supabase.com
2. Projeto > **Settings** > **API**
3. Copie o valor de **service_role** (secret)

### Passo 2: Adicionar ao .env
Crie ou edite o arquivo `.env.local`:
```
VITE_SUPABASE_URL=https://seu-projeto.supabase.co
VITE_SUPABASE_ANON_KEY=seu_anon_key
SUPABASE_SERVICE_ROLE_KEY=seu_service_role_key
```

### Passo 3: Executar Script
```bash
node scripts/setup-storage.mjs
```

Vai aparecer:
```
✓ Bucket criado com sucesso!
✓ Setup de Storage concluído com sucesso!
```

---

## ✅ **Verificar se Funcionou**

### No Supabase Dashboard:
1. Vá em **Storage** > **Buckets**
2. Você deve ver: `product-images` (Public)

### No App:
1. Acesse `/editar-loja`
2. Selecione uma imagem de logo
3. Clique em "Criar Loja"
4. **Console (F12)** deve mostrar:
   ```
   ✓ "Upload bem-sucedido: https://..."
   ```

---

## 🆘 **Se Continuar Falhando**

### Verificar Políticas RLS

1. Vá em **Storage** > **Policies**
2. Procure por policies que começam com `product_images_`
3. Se não existirem, execute no **SQL Editor**:

```sql
DROP POLICY IF EXISTS "product_images_public_read" ON storage.objects;
DROP POLICY IF EXISTS "product_images_owner_upload" ON storage.objects;
DROP POLICY IF EXISTS "product_images_owner_update" ON storage.objects;
DROP POLICY IF EXISTS "product_images_owner_delete" ON storage.objects;

CREATE POLICY "product_images_public_read"
ON storage.objects FOR SELECT
USING (bucket_id = 'product-images');

CREATE POLICY "product_images_owner_upload"
ON storage.objects FOR INSERT TO authenticated
WITH CHECK (bucket_id = 'product-images' AND (storage.foldername(name))[1] = auth.uid()::text);

CREATE POLICY "product_images_owner_update"
ON storage.objects FOR UPDATE TO authenticated
USING (bucket_id = 'product-images' AND (storage.foldername(name))[1] = auth.uid()::text)
WITH CHECK (bucket_id = 'product-images' AND (storage.foldername(name))[1] = auth.uid()::text);

CREATE POLICY "product_images_owner_delete"
ON storage.objects FOR DELETE TO authenticated
USING (bucket_id = 'product-images' AND (storage.foldername(name))[1] = auth.uid()::text);
```

---

## 🚀 **Resumo**

```
1. Crie o bucket via Dashboard ✓
2. Execute as SQL policies ✓
3. Teste upload de imagem ✓
4. Pronto para usar! 🎉
```
