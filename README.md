# KARASU NERO (カラス・ネロ) — Dark Luxury Menswear Monorepo

> **Quiet Luxury × Old Money × Italian Tailoring × Japanese Discipline**  
> متجر إلكتروني فاخر متكامل مع لوحة تحكم إدارية باللغة العربية (RTL) ونظام دفع عند الاستلام (COD) مخصص للسوق الجزائري مع تتبع دقيق للمخزون ومنع الـ Overselling.

---

## 🏛️ هيكل المشروع (Monorepo Architecture)

```
karasu/
├── apps/
│   ├── store/               # متجر الزبائن (Next.js 14 App Router, Dark Luxury Editorial, Cart Drawer, COD Checkout)
│   └── admin/               # لوحة تحكم الإدارة (Next.js 14 App Router, Arabic RTL, Inventory Ledger, Orders, Analytics)
│
├── packages/
│   ├── types/               # تعريفات TypeScript المشتركة لجميع الكيانات
│   ├── database/            # استعلامات Supabase، دوال الـ RPC، ومخططات Zod
│   ├── ui/                  # مكونات التصميم الأساسية وشعار الغراب الفاخر (Raven Logo)
│   └── utils/               # بيانات الـ 58 ولاية جزائرية، التحقق من أرقام الهواتف، وتنسيق العملة DZD
│
└── supabase/
    ├── migrations/          # مخططات SQL، دوال المعاملات الذرية (RPC)، وسياسات الأمان RLS
    └── seed/                # البيانات الأولية للمنتجات، الولايات الـ 58، والإعدادات
```

---

## ⚡ التشغيل السريع (Quick Start)

### 1. تثبيت الحزم:
```bash
npm install
```

### 2. تجميع الحزم المشتركة:
```bash
npm run build --workspace=@karasu/types
npm run build --workspace=@karasu/utils
npm run build --workspace=@karasu/database
npm run build --workspace=@karasu/ui
```

### 3. تشغيل متجر الزبائن (Storefront - Port 3000):
```bash
npm run dev:store
```
افتح المتصفح على: `http://localhost:3000`

### 4. تشغيل لوحة تحكم الإدارة (Admin Dashboard - Port 3001):
```bash
npm run dev:admin
```
افتح المتصفح على: `http://localhost:3001`

---

## 🗄️ إعداد قاعدة بيانات Supabase

1. أنشئ مشروعاً جديداً في [Supabase](https://supabase.com).
2. افتح **SQL Editor** في لوحة Supabase وقم بتشغيل ملف المخطط الأساسي:
   - `supabase/migrations/20260926000000_init_schema.sql`
3. قم بتشغيل ملف البيانات الأولية لملء الـ 58 ولاية والمنتجات الفاخرة:
   - `supabase/seed/seed.sql`
4. انسخ ملف `.env.example` إلى `.env.local` في مجلدي `apps/store` و `apps/admin` وضع المفاتيح:
   ```env
   NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
   NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
   SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
   ```

---

## 🛡️ منطق حجز المخزون ومنع الـ Overselling

- عند إنشاء الزبون للطلب من صفحة الدفع الجزائري (COD):
  - يتم فحص الكمية المتاحة ذرياً (`stock_quantity - reserved_quantity >= requested_qty`).
  - يتم حجز الكمية `reserved_quantity + qty` فورياً لمنع طلبها من زبون آخر.
- عند قيام المسؤول في لوحة التحكم بتأكيد الطلب (`CONFIRMED` أو `PREPARING`):
  - يتم خصم المخزون الحقيقي `stock_quantity - qty` وفك الحجز `reserved_quantity - qty`.
  - يتم توثيق حركة الخصم في جدول تدقيق الحركات `inventory_movements`.
- عند إلغاء الطلب (`CANCELLED`):
  - يتم فك الحجز التلقائي وإعادة القطعة إلى المتاح للبيع فورياً.
- عند إرجاع الطلب من شركة الشحن (`RETURNED`):
  - يُعاد إدراج القطعة للمخزون الحقيقي مع تسجيل الحركة كـ `return_restock`.

---

## 🇩🇿 مميزات نظام الشراء الجزائري (Algerian COD)

1. **دفع عند الاستلام**: لا يتطلب بطاقة بنكية أو حساب إجباري.
2. **التحقق من الهواتف**: دعم دقيق لصيغ متعاملي الهاتف في الجزائر (موبيليس `06`، جيزي `07`، أوريدو `05`).
3. **58 ولاية جزائرية**: قاعدة بيانات كاملة للولايات والبلديات مع تسعير منفصل للتوصيل لباب المنزل (Domicile) ومكتب التوصيل (Stopdesk).
4. **تكامل فوري مع WhatsApp**: توليد رابط محادثة معبأ مسبقاً يحتوي على رقم الطلب واسم الزبون والمبلغ الإجمالي لتسريع التأكيد.
"# testoussama" 
