# Mi Campeonato F1: cómo publicarlo

La carpeta tiene tres archivos:

- `index.html`: la página.
- `config.js`: donde pegás los datos de tu base de datos (Supabase).
- `LEEME.md`: esta guía.

Todos ven el campeonato. Solo vos podés cargar carreras, entrando como admin.

## 1. Crear la base de datos (Supabase, gratis)

1. Entrá a https://supabase.com, creá una cuenta y un proyecto nuevo.
2. En el menú de la izquierda abrí **SQL Editor**, pegá esto, cambiá `TU_EMAIL` por tu email y tocá **Run**:

```sql
create table if not exists public.f1_docs (
  id text primary key,
  kind text not null,
  data jsonb not null,
  updated_at timestamptz not null default now()
);

alter table public.f1_docs enable row level security;

create policy "todos pueden leer"
  on public.f1_docs for select
  to anon, authenticated
  using (true);

create policy "solo el admin escribe"
  on public.f1_docs for all
  to authenticated
  using ((auth.jwt() ->> 'email') = 'TU_EMAIL')
  with check ((auth.jwt() ->> 'email') = 'TU_EMAIL');
```

3. Andá a **Authentication > Users > Add user > Create new user**. Poné tu email y una contraseña, y marcá **Auto Confirm User**. Con ese email y contraseña entrás como admin en la página.
4. Andá a **Authentication > Sign In / Providers** y apagá **Allow new users to sign up**, para que nadie más pueda crearse una cuenta.

## 2. Pegar los datos en config.js

1. En Supabase abrí **Project Settings > API** (o **API Keys**).
2. Copiá la **Project URL** y la clave **anon** (o **publishable**).
3. Abrí `config.js` y reemplazá los dos valores de ejemplo. Esa clave es pública, está bien que figure en la página.

## 3. Subir a Vercel

Opción rápida: con Node instalado, abrí una terminal dentro de la carpeta y ejecutá `npx vercel --prod`.

Opción con GitHub: subí la carpeta a un repositorio, importalo en https://vercel.com/new y dejá todo por defecto. No hace falta configurar build.

Vercel te da un link público para pasarles a tus amigos.

## 4. Usarlo

1. Abrí tu link y tocá **Entrar** abajo de todo. Ingresá el email y la contraseña del paso 1.
2. Tocá **Cargar carrera** y tocá a los pilotos en el orden en que llegaron.
3. Tus amigos ven los cambios sin hacer nada. La página se refresca sola cada 30 segundos.

## Que aparezca en Google

El link funciona apenas lo subís. Para que Google lo muestre en las búsquedas, entrá a https://search.google.com/search-console, agregá tu sitio y pedí que lo indexen. Puede tardar días y Google decide si lo muestra.
