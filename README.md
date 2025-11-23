# Supamailer

Supamailer is a SaaS platform for sending transactional and marketing emails from verified custom domains. The system is built on AWS SES.

**Live:** [supamailer.online](https://supamailer.online)

---

# Features

- **Domain Management**  
  Add custom domains, generate DNS records, and fully verify DKIM, SPF, and MAIL FROM.

- **Email Sending**  
  Send emails via REST API or admin panel.

- **Event Tracking**  
  Track deliveries, bounces, complaints, rejections, and other AWS SES events.

- **Subscription Plans**  
  Sending limits depend on the user plan, billed via Stripe.

- **API Keys**  
  Authorize sending emails from the user’s applications.

---

# Tech Stack

**Backend:** Express.js, TypeScript, Prisma, PostgreSQL, Stripe, AWS SDK  
**Frontend:** Next.js, Tailwind CSS, shadcn/ui, React Hook Form, Zod  
**Deploy:** Coolify  

---

# Upcoming Features

- Improved homepage and login screens  
- Documentation and informational pages  
- Bulk sending + Redis queues  
- Email template editor  
- Audience lists for marketing campaigns  
- User webhooks  

---

# Key Implementation Details

## Authorization

**JWT (for frontend)**

- Backend generates JWT on login  
- Next.js stores the token in **cookie**  
- Middleware protects dashboard pages  
- Backend validates tokens using `jose`  
- JWT contains `userId` and expiration time  

**API Key (for integration)**

- API Key authorizes requests sent directly from the user’s application.  
- Generation process:
  1. Backend generates a full secret key (shown **only once**), a short prefix, and a hash.  
  2. Database stores the prefix, hash, and key name.  
  3. User receives the full key.  
  4. Each request:
     - splits key into prefix and rawKey  
     - finds record by prefix  
     - hashes rawKey and compares with stored hash  

---

## Adding Domains

1. User adds a domain  
2. Backend generates DKIM, SPF, MAIL FROM records  
3. Records are added to AWS SES  
4. User receives DNS instructions  
5. Backend checks verification status  

Before verification, sending is allowed only from the temporary address `dev@supamailer.online`.

---

## Sending Emails

1. Backend calls AWS SES  
2. SES processes the message  
3. SES publishes an event to SNS  
4. SNS triggers the backend webhook  
5. Webhook:
   - verifies SNS signature  
   - saves event (delivered, bounced, complaint, rejected)  
   - updates sending status  

Users can view the full email history in the dashboard.

---

## User Limits

- Middleware checks the user plan and sent emails  
- Sending is blocked if the limit is exceeded  

---

## Forms and Validation

- React Hook Form handles forms  
- Zod validates data on both client and backend  

---

# Supamailer (PL)

Supamailer to platforma SaaS do wysyłania e-maili transakcyjnych i marketingowych z własnych, zweryfikowanych domen. System korzysta z AWS SES.

**Live:** [supamailer.online](https://supamailer.online)

---

# Najważniejsze funkcje

- **Zarządzanie domenami**  
  Dodawanie własnych domen, generowanie rekordów DNS oraz pełna weryfikacja DKIM, SPF i MAIL FROM.

- **Wysyłanie e-maili**  
  Możliwość wysyłki poprzez REST API lub panel administracyjny.

- **Śledzenie zdarzeń**  
  Informacje o dostarczeniach, odrzuceniach, skargach, bounce i innych eventach AWS SES.

- **Plany subskrypcyjne**  
  Limity wysyłek zależne od planu użytkownika, rozliczane przez Stripe.

- **System kluczy API**  
  Autoryzacja wysyłek z aplikacji użytkownika.

---

# Stack technologiczny

**Backend:** Express.js, TypeScript, Prisma, PostgreSQL, Stripe, AWS SDK  
**Frontend:** Next.js, Tailwind CSS, shadcn/ui, React Hook Form, Zod  
**Deploy:** Coolify  

---

# Planowane funkcje

- Usprawnienie strony głównej i ekranów logowania  
- Dokumentacja i strony informacyjne  
- Masowe wysyłki + kolejki Redis  
- Edytor szablonów e-mail  
- Listy odbiorców dla kampanii marketingowych  
- Webhooki użytkownika  

---

# Wybrane aspekty tworzenia platformy

## Autoryzacja

**JWT (dla frontendu)**

- Backend generuje JWT podczas logowania  
- Next.js zapisuje token w **cookie**  
- Middleware chroni strony dashboardu  
- Backend waliduje tokeny przy użyciu `jose`  
- Token zawiera `userId` i czas wygaśnięcia  

**API Key (do integracji)**

- Klucz API autoryzuje zapytania wysyłane z aplikacji użytkownika.  
- Proces generowania:
  1. Backend generuje pełny klucz (pokazywany **tylko raz**), prefix i hash  
  2. Baza przechowuje prefix, hash i nazwę klucza  
  3. Użytkownik otrzymuje pełny klucz  
  4. Każdy request:
     - rozdziela klucz na prefix i rawKey  
     - wyszukuje rekord po prefixie  
     - hashuje rawKey i porównuje z hashem w bazie  

---

## Dodawanie domen

1. Użytkownik dodaje domenę  
2. Backend generuje rekordy DKIM, SPF, MAIL FROM  
3. Rekordy dodawane są do AWS SES  
4. Użytkownik otrzymuje instrukcje DNS  
5. Backend sprawdza status weryfikacji  

Do momentu weryfikacji wysyłka możliwa tylko z tymczasowego adresu `dev@supamailer.online`.

---

## Wysyłanie wiadomości e-mail

1. Backend wywołuje AWS SES  
2. SES przetwarza wiadomość  
3. SES publikuje event do SNS  
4. SNS wywołuje webhook backendu  
5. Webhook:
   - weryfikuje podpis SNS  
   - zapisuje event (delivered, bounced, complaint, rejected)  
   - aktualizuje status wysyłki  

Użytkownik widzi pełną historię wysyłek w panelu.

---

## Limity użytkownika

- Middleware sprawdza plan i liczbę wysłanych wiadomości  
- Jeśli limit przekroczony, wysyłka zostaje zablokowana  

---

## Formularze i walidacja

- React Hook Form obsługuje formularze  
- Zod waliduje dane po stronie klienta i backendu  
