# Quiz Platform (Django, শুধু Python + SQL + HTML + CSS)

React, Node.js, JavaScript বা Bootstrap কিছুই ব্যবহার হয়নি।
কাউন্টডাউন টাইমারও শুধু CSS দিয়ে চলে।

## VS Code-এ চালানোর নিয়ম (Windows)

ZIP Extract করে VS Code-এ ফোল্ডারটি Open করো। তারপর Terminal-এ একটি একটি করে চালাও:

```
py -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
python manage.py migrate
python manage.py seed_demo
python manage.py runserver
```

(Python 3.10 বা তার নতুন ভার্সন লাগবে।)

তারপর Browser-এ:

| কী | লিংক |
|---|---|
| ওয়েবসাইট (Welcome পেজ) | http://127.0.0.1:8000/ |
| Student Login | http://127.0.0.1:8000/login/ |
| Admin Login | http://127.0.0.1:8000/panel/login/ |
| Django-র নিজস্ব Admin (শুধু টেকনিক্যাল ব্যাকআপ) | http://127.0.0.1:8000/django-admin/ |

`seed_demo` কমান্ড ডেমো ডেটা বানায়: ৩টি Category, ৩০টি প্রশ্ন, ৪টি Quiz এবং দুটি ডেমো অ্যাকাউন্ট:

| Role | Email | Password |
|---|---|---|
| Admin | admin@quiz.local | Admin@12345 |
| Student | student1@quiz.local | Student@12345 |

নিজের Admin বানাতে চাইলে: `python manage.py createsuperuser`
**ওয়েবসাইট অনলাইনে দেওয়ার আগে ডেমো পাসওয়ার্ড বদলাও (বা ডেমো অ্যাকাউন্ট মুছে দাও)।**
ডেমো ডেটা না চাইলে `seed_demo` চালিও না।

## কী কী আছে

**Student / Guest**
- Welcome পেজ (অনুপ্রেরণামূলক লেখা + Start Quiz), তারপর Category অনুযায়ী Quiz বাছাই
- Registration (Username, নাম, Email, Password) ও Login (**Email দিয়ে**)
- Login ছাড়াই Guest হিসেবে Quiz দেওয়া যায় (নাম লিখতে হয়)। Guest-এর Result Database-এ সেভ থাকে এবং Leaderboard-এ "Guest" ব্যাজসহ দেখায়
- Guest-এর Quiz শেষে একটি Popup আসে: Registration/Login করলে ওই ব্রাউজারের Guest Attempt-গুলো নতুন Account-এ যুক্ত হয়ে যায়। Cancel করতে "এখন নয়" বা ✕ আছে
- উপরের ডান কোণে Login ও Registration বাটন
- Student Dashboard: Available Quizzes, History, মোট Attempt, Correct/Wrong/Unanswered, Current Rank, Leaderboard, Profile
- Result Page: মোট স্কোর, Answer Review, সঠিক উত্তর ও Explanation (Quiz শেষ হওয়ার আগে সঠিক উত্তর কখনো দেখানো হয় না)

**Quiz নিয়ম**
- ডিফল্ট: ১০টি প্রশ্ন, প্রতি প্রশ্নে ৩০ সেকেন্ড (মোট ৫ মিনিট), সঠিক +১, ভুল −০.৫, উত্তর না দিলে ০
- প্রতিবার Random প্রশ্ন, একই Attempt-এ কোনো প্রশ্ন দুইবার আসে না (Database Constraint-ও আছে)
- Retake করলে নতুন Random সেট। প্রতিটি Quiz-এ Retake অনুমতি Admin ঠিক করতে পারে
- যথেষ্ট Active প্রশ্ন না থাকলে Quiz শুরু বা Publish করা যায় না

**Timer কীভাবে চলে (JavaScript ছাড়া)**
- একবারে একটি প্রশ্ন। অপশনে ক্লিক করলে উত্তর সাথে সাথে সেভ হয়, কিন্তু একই প্রশ্নেই থাকে। ৩০ সেকেন্ডের মধ্যে যতবার খুশি উত্তর বদলানো যায়
- ৩০ সেকেন্ড শেষ হলে পেজ নিজে থেকে রিফ্রেশ হয়ে পরের প্রশ্ন আসে। শেষ প্রশ্নের পর Quiz Auto Submit হয়
- লাইভ সংখ্যার কাউন্টডাউন ও কমতে থাকা বার শুধু CSS (`@property` + `counter()`) দিয়ে চলে। শেষ ১০ সেকেন্ডে সংখ্যা লাল হয়
- আসল নিয়ন্ত্রণ সার্ভারে: "এখন কোন প্রশ্ন" সার্ভার সময় থেকে হিসাব করে। তাই পুরোনো প্রশ্নের উত্তর বদলানো বা বেশি সময় নেওয়া যায় না
- দরকার: Chrome/Edge 85+, Safari 16.4+, Firefox 128+ (নতুন ব্রাউজারে সংখ্যা ঘোরে। খুব পুরোনো ব্রাউজারে সংখ্যা ঘুরবে না, তবে ৩০ সেকেন্ড পরে প্রশ্ন বদলানো ঠিকই কাজ করবে)

**Leaderboard**
- বেশি স্কোর আগে, স্কোর সমান হলে বেশি Correct, তারপর কম সময়
- প্রতিজন Student সেরা Attempt নিয়ে একবার থাকে, Quiz অনুযায়ী ফিল্টার আছে

**Admin Panel (`/panel/`)**
- Dashboard, Student (খোঁজা, দেখা, Edit, Disable/Enable, Delete), Category, Question (ম্যানুয়াল যোগ/Edit/Delete/Active-Inactive, ফিল্টার), Quiz (তৈরি, Publish/Unpublish, সময়, Negative Mark, Retake), Result (Quiz/Student অনুযায়ী ও Answer Review)
- **Excel Upload:** Template ডাউনলোড, `.xlsx` আপলোড, Preview, Error Report, ঠিক ডেটা একসাথে যোগ। Excel-এ নতুন Category নাম থাকলে নিজে তৈরি হয়
- Admin-এর জন্য আলাদা Login, Sign up নেই। Student `/panel/` খুললে 403 পাবে

**ভাষা:** উপরের কোণে বাংলা / EN সুইচ (ডিফল্ট বাংলা)। নতুন বাক্য অনুবাদ করতে `quiz/translations.py`-তে একটি লাইন যোগ করলেই হয়। ডিফল্ট ভাষা বদলাতে `config/settings.py`-এ `DEFAULT_SITE_LANGUAGE` বদলাও।

## Excel ফাইলের ফরম্যাট

| Question | Option A | Option B | Option C | Option D | Correct Answer | Category | Explanation |
|---|---|---|---|---|---|---|---|
| বাংলাদেশের রাজধানী কী? | চট্টগ্রাম | ঢাকা | সিলেট | খুলনা | B | সাধারণ জ্ঞান | ঢাকা রাজধানী |

Correct Answer হবে A, B, C বা D। Explanation ফাঁকা রাখা যায়।

## প্রজেক্টের ফাইল কোথায় কী

| ফাইল | কাজ |
|---|---|
| `quiz/models.py` | Database টেবিল (User, Category, Question, Quiz, Attempt, AttemptAnswer) |
| `quiz/views.py` | Student/Guest-এর পেজ (Quiz দেওয়া, Result, Leaderboard) |
| `quiz/panel_views.py` | Admin Panel |
| `quiz/services.py` | Random প্রশ্ন, স্কোর, Leaderboard, Excel Import |
| `quiz/translations.py` | বাংলা অনুবাদ |
| `templates/` | HTML পেজ |
| `static/css/style.css` | সব CSS ও কাউন্টডাউন |
| `quiz/tests.py` | স্বয়ংক্রিয় টেস্ট (`python manage.py test`) |

## পরে MySQL ব্যবহার করতে চাইলে
`pip install mysqlclient` করে `config/settings.py`-এর `DATABASES` MySQL-এর তথ্য দিয়ে বদলাও, তারপর `python manage.py migrate` চালাও।

## অনলাইনে (Deploy) দেওয়ার আগে
- `config/settings.py`-এ `DEBUG = False`, নিজের `SECRET_KEY` এবং `ALLOWED_HOSTS`-এ ডোমেইন বসাও
- `python manage.py collectstatic` চালাও
- ডেমো পাসওয়ার্ড বদলাও
