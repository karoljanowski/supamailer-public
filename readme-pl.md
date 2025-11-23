# **Supamailer**

Supamailer to platforma SaaS do wysyłania e-maili transakcyjnych i marketingowych z własnych, zweryfikowanych domen. System opiera się na AWS SES.

---

# **Najważniejsze funkcje**

- **Zarządzanie domenami** – dodawanie własnych domen, generowanie rekordów DNS oraz pełna weryfikacja DKIM, SPF i MAIL FROM  
- **Wysyłanie e-maili** – poprzez REST API lub panel administracyjny  
- **Śledzenie zdarzeń** – informacje o dostarczeniach, odrzuceniach, skargach, bounce i innych eventach AWS SES  
- **Plany subskrypcyjne** – limity wysyłek zależne od planu użytkownika, rozliczane poprzez Stripe
- **System kluczy API**  

---

# **Stack technologiczny**

**Backend:** Express.js, TypeScript, Prisma, PostgreSQL, Stripe, AWS SDK  
**Frontend:** Next.js, Tailwind CSS, shadcn/ui, React Hook Form, Zod  
**Deploy:** Coolify  

---

# **Planowane funkcje**

- usprawnienie strony głównej i ekranów logowania  
- dokumentacja i strony informacyjne  
- masowe wysyłki + kolejki Redis  
- edytor szablonów e-mail  
- listy odbiorców dla kampanii marketingowych  
- webhooki użytkownika  

---

# **Wybrane aspekty tworzenia platformy**

**Autoryzacja**

**JWT (dla frontendu)**

- backend generuje JWT podczas logowania  
- Next.js zapisuje token w **cookie**  
- middleware Next.js sprawdza, czy cookie istnieje i chroni strony dashboardu  
- backend waliduje tokeny przy użyciu `jose`  
- token zawiera m.in. `userId` i ma określony czas wygaśnięcia  



**API Key (do integracji z aplikacją użytkownika)**

API Key służy do autoryzacji zapytań wykonywanych bezpośrednio z aplikacji użytkownika, np. przy wysyłce e-maili z ich platformy.

**Proces generowania klucza API:**

1. Backend generuje:
   - pełny tajny klucz (pokazywany **tylko raz**)  
   - krótki prefix  
   - hash klucza  
2. Do bazy trafiają:
   - prefix  
   - hash klucza  
   - nazwa klucza  

3. Użytkownik otrzymuje pełny klucz.

4. Każdy request:
- rozdziela klucz na prefix i rawKey  
- wyszukuje rekord po prefixie  
- hashuje rawKey i porównuje z hashem z bazy  

Prefix umożliwia szybkie odnalezienie klucza.

---

**Dodawanie domen**

1. Użytkownik dodaje domenę  
2. Backend generuje rekordy:
- DKIM  
- SPF  
- MAIL FROM  
3. Rekordy są dodawane do AWS SES  
4. Użytkownik otrzymuje instrukcje DNS  
5. Backend sprawdza status weryfikacji w AWS SES  

Do momentu weryfikacji wysyłka jest możliwa tylko z adresu tymczasowego `dev@supamailer.online`.

---

**Wysyłanie wiadomości e-mail**

1. Backend wywołuje AWS SES  
2. SES przetwarza wiadomość  
3. SES publikuje event do SNS  
4. SNS wywołuje webhook backendu  
5. Webhook:
- weryfikuje podpis SNS  
- zapisuje event (delivered, bounced, complaint, rejected, itd.)  
- aktualizuje status wysyłki  

Użytkownik widzi pełną historię wysyłek w panelu.

---

**Limity użytkownika**

1. Każdy użytkownik ma przypisany plan  
2. Middleware przed wysyłką sprawdza:
- aktualny plan  
- liczbę wysłanych wiadomości  
3. Jeśli limit został przekroczony — wysyłka zostaje zablokowana  

---

**Formularze i walidacja**

React Hook Form odpowiada za obsługę formularzy. Każde przesłanie formularza jest walidowane za pomocą Zod:
- natychmiast po stronie klienta
- ponownie walidowane na backendzie
---
