# Mongoose-Validations-A-to-Z
English and Bangla Explanation

Mongoose Validation কি?

Mongoose হল Node.js এর জন্য MongoDB ODM লাইব্রেরি। Validation মানে হলো ডেটা ডাটাবেজে যাওয়ার আগে যাচাই করে দেখা যেন তা সঠিক নিয়মে আছে কি না।

উদাহরণ:
const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  age: { type: Number, min: 18 }
});
এখানে name field টি required, এবং age কমপক্ষে ১৮ হতে হবে।

Validation কি?
ডাটাবেসে ডাটা সেভ করার আগে তা যাচাই করা। যেমন: ইমেইল ফর্ম্যাট সঠিক কিনা, নামের দৈর্ঘ্য ঠিক আছে কিনা ইত্যাদি।

Mongoose-এ Validation দুই ধরনের:

Built-in Validations: Mongoose-এর নিজস্ব ভ্যালিডেশন (required, min, max, enum, match ইত্যাদি)।

Custom Validations: ডেভেলপার নিজে তৈরি করে (validate ফাংশন ব্যবহার করে)।

Built-in Validations:
Mongoose Schema-তে ডিফাইন করা যায়:

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true, // নাম অবশ্যই দিতে হবে
    minlength: 3,   // নাম কমপক্ষে ৩ অক্ষর
    maxlength: 30    // নাম সর্বোচ্চ ৩০ অক্ষর
  },
  age: {
    type: Number,
    min: 18,         // বয়স কমপক্ষে ১৮
    max: 100         // বয়স সর্বোচ্চ ১০০
  }
});
Custom Validations:
validate প্রপার্টি ব্যবহার করে:

const productSchema = new mongoose.Schema({
  price: {
    type: Number,
    validate: {
      validator: function(value) {
        return value >= 100; // দাম ১০০ টাকার বেশি হতে হবে
      },
      message: 'Price must be at least 100 TK' // এরর মেসেজ
    }
  }
});

১. required validator
name: { type: String, required: true }
👉 name না দিলে error দিবে: "Path name is required."

২. min এবং max
age: { type: Number, min: 18, max: 60 }
👉 বয়স ১৮-৬০ এর বাইরে হলে error দিবে।

৩. maxlength এবং minlength
username: { type: String, minlength: 5, maxlength: 15 }
👉 username এর দৈর্ঘ্য ৫-১৫ অক্ষরের মধ্যে হতে হবে।

৪. enum validator
role: { type: String, enum: ['admin', 'user', 'manager'] }
👉 role এর মান শুধু এই তিনটা হতে পারবে।

৫. match validator (Regex)
email: { type: String, match: /.+\@.+\..+/ }
👉 ইমেইল ফরম্যাট না হলে error দিবে।

৬. Custom Validator (Function দিয়ে)
password: {
  type: String,
  validate: {
    validator: function(v) {
      return v.length >= 6;
    },
    message: 'পাসওয়ার্ড কমপক্ষে ৬ অক্ষরের হতে হবে।'
  }
}
৭. Async Custom Validator
email: {
  type: String,
  validate: {
    validator: async function(email) {
      const count = await this.model('User').countDocuments({ email });
      return count === 0;
    },
    message: 'এই ইমেইলটি আগে থেকেই ব্যবহার করা হয়েছে।'
  }
}
৮. validateBeforeSave বন্ধ করা
user.validateBeforeSave = false;
👉 validation না চালিয়ে সরাসরি save করা যাবে (not recommended)।

৯. Array Validation
tags: {
  type: [String],
  validate: [arrayLimit, 'কমপক্ষে ১টি tag দিতে হবে।']
}
function arrayLimit(val) {
  return val.length > 0;
}
১০. Nested Object Validation
address: {
  street: { type: String, required: true },
  city: { type: String }
}

Project 1: Student Registration
const studentSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, match: /.+@.+\..+/ },
  age: { type: Number, min: 10, max: 25 }
});
👉 কাজ: ভুল ডেটা দিলে insert হবে না।

📌 Project 2: Product Catalog
const productSchema = new mongoose.Schema({
  title: { type: String, required: true },
  price: { type: Number, min: 1 },
  category: { type: String, enum: ['electronics', 'fashion', 'grocery'] }
});
📌 Project 3: Secure Password Checker
const userSchema = new mongoose.Schema({
  password: {
    type: String,
    validate: {
      validator: function(p) {
        return p.length >= 8;
      },
      message: "পাসওয়ার্ড কমপক্ষে ৮ অক্ষরের হতে হবে।"
    }
  }
});

❌ ভুল: required: true না দেওয়া → ডাটা মিসিং হতে পারে।
✅ সমাধান: required: [true, "Error message"] ব্যবহার করুন।

Email Validator এর উদাহরণ:
email: {
  type: String,
  match: /.+\@.+\..+/
}
⬇ ব্যাখ্যা:
অংশ	মানে
.+	এক বা একাধিক অক্ষর থাকতে হবে
\@	একটি @ চিহ্ন থাকতে হবে
\.	একটি . (dot) থাকতে হবে
সবশেষে .+	আবার এক বা একাধিক অক্ষর থাকতে হবে

📧 যেমন:

✅ azad@gmail.com → Valid

❌ azadgmail.com → Invalid (কারণ @ নেই)

❌ azad@com → Invalid (কারণ ডট নেই)

বেসিক ইমেইল ভ্যালিডেশন
const userSchema = new mongoose.Schema({
  email: {
    type: String,
    match: /.+\@.+\..+/, // সাধারণ ইমেইল ফরম্যাট চেক
    required: true
  }
});
ব্যাখ্যা:

/.+\@.+\..+/ → এই Regex প্যাটার্নটি নিম্নলিখিত ফরম্যাট চেক করে:

.+ = এক বা একাধিক যেকোনো অক্ষর

\@ = @ চিহ্ন (ইমেইলের জন্য অবশ্যই দরকার)

.+ = এক বা একাধিক অক্ষর (ডোমেইন নাম)

\. = . (ডট)

.+ = এক বা একাধিক অক্ষর (TLD যেমন: .com, .net)

যেমন:

ভালো ইমেইল: test@example.com ✅

খারাপ ইমেইল: test@com ❌ (ডট নেই)

স্ট্রিক্ট ইমেইল ভ্যালিডেশন
email: {
  type: String,
  match: /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/,
  required: true
}
ব্যাখ্যা:

^[a-zA-Z0-9._%+-]+ = ইমেইলের প্রথম অংশে শুধু লেটার, নম্বর, . _ % + - অক্ষর allowed

@[a-zA-Z0-9.-]+ = @ এর পরে ডোমেইন নাম (লেটার, নম্বর, . -)

\.[a-zA-Z]{2,}$ = .com, .net ইত্যাদি (অন্তত ২ অক্ষরের TLD)

যেমন:

ভালো ইমেইল: user.name+123@example.co.uk ✅

খারাপ ইমেইল: user@.com ❌ (ডোমেইন নেই)

৩. ফোন নাম্বার ভ্যালিডেশন (বাংলাদেশি ফরম্যাট)
phone: {
  type: String,
  match: /^(?:\+88|01)?(?:\d{11}|\d{13})$/, // +880 বা 01 দিয়ে শুরু
  required: true
}
ব্যাখ্যা:

^(?:\+88|01)? = +88 বা 01 দিয়ে শুরু (optional)

(?:\d{11}|\d{13})$ = ১১ বা ১৩ ডিজিট (বাংলাদেশি ফোন নাম্বার)

যেমন:

ভালো নাম্বার: +8801712345678, 01712345678 ✅

খারাপ নাম্বার: 12345678 ❌ (ফরম্যাট ভুল)

টাস্ক: নিচের Schema-তে password ফিল্ড যোগ করুন যেখানে:

অন্তত ৮ অক্ষর

একটি বড় হাতের অক্ষর (A-Z)

একটি ছোট হাতের অক্ষর (a-z)

একটি সংখ্যা (0-9)

সমাধান:
password: {
  type: String,
  match: [
    /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{8,}$/,
    "Password must be 8+ chars, with 1 uppercase, 1 lowercase, and 1 number!"
  ]
}
ব্যাখ্যা:

(?=.*[a-z]) = অন্তত ১টি ছোট হাতের অক্ষর

(?=.*[A-Z]) = অন্তত ১টি বড় হাতের অক্ষর

(?=.*\d) = অন্তত ১টি সংখ্যা

.{8,} = ৮ বা বেশি অক্ষর
