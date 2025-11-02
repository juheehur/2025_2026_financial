# Expense Tracker

A modern expense tracking web application with AI-powered receipt scanning. Track your expenses in Thai Baht (THB) with automatic extraction from receipt images.

## Features

- 📝 Manual expense entry
- 📷 AI-powered receipt scanning using Gemini Vision API
- 🤖 Automatic amount and description extraction from receipt images
- 💾 Cloud storage with Supabase
- 🖼️ Receipt image storage (required)
- ✅ Payback tracking - mark expenses as paid back
- 📊 Total expense summary (excludes payback items)
- 🌐 Support for English and Thai language receipts
- 📱 Mobile-friendly clean design

## Tech Stack

- Next.js 15
- React 19
- TypeScript
- Tailwind CSS
- Google Gemini Vision API (Image OCR)
- Supabase (Database)

## Installation

```bash
npm install
```

## Environment Variables

Create a `.env.local` file in the root directory and add the following:

```env
NEXT_PUBLIC_GEMINI_API_KEY=your_gemini_api_key_here
NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
```

### Getting API Keys

1. **Gemini API Key**: Get your API key from [Google AI Studio](https://makersuite.google.com/app/apikey)
2. **Supabase**: 
   - Create a project at [Supabase](https://supabase.com)
   - Go to Project Settings > API
   - Copy your Project URL and anon/public key

## Database Setup

### 1. Create Expenses Table

Create a table named `expenses` in your Supabase database with the following schema:

```sql
create table expenses (
  id uuid default gen_random_uuid() primary key,
  amount numeric not null,
  description text not null,
  category text,
  receipt_image_url text not null,
  payback boolean default false,
  created_at timestamp with time zone default timezone('utc'::text, now()) not null
);

-- Enable Row Level Security (optional, for production)
alter table expenses enable row level security;

-- Create a policy to allow all operations (adjust as needed for your use case)
create policy "Allow all operations" on expenses
  for all using (true) with check (true);
```

**Note**: If you already have an `expenses` table, you can add the `payback` column with:

```sql
alter table expenses add column payback boolean default false;
alter table expenses alter column receipt_image_url set not null;
```

### 2. Create Storage Bucket

Create a storage bucket named `receipts` in Supabase:

1. Go to Storage in your Supabase dashboard
2. Click "New bucket"
3. Name it `receipts`
4. Make it **public** (so images can be accessed)
5. Click "Create bucket"

Alternatively, you can use SQL:

```sql
-- Create storage bucket
insert into storage.buckets (id, name, public)
values ('receipts', 'receipts', true);

-- Create storage policy to allow public access
create policy "Public Access" on storage.objects
  for select using (bucket_id = 'receipts');

create policy "Public Upload" on storage.objects
  for insert with check (bucket_id = 'receipts');
```

You can run these SQL commands in the Supabase SQL Editor.

## Running the Application

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

## Usage

1. **Manual Entry**: 
   - Enter the amount and description
   - **Attach a receipt image (required)**
   - Click "Add Expense"
2. **Receipt Scanning**: 
   - Click the "Scan" button
   - Take a photo or select an image of your receipt
   - The app uses Gemini Vision API to automatically extract:
     - Amount in Thai Baht
     - Description in English
   - Review and add the expense
3. **Payback Tracking**: 
   - Check the checkbox next to an expense to mark it as paid back
   - Payback items are excluded from the total expense calculation
   - Payback items are visually highlighted with green border and strikethrough

## How It Works

1. **Image Processing**: When you upload a receipt image, it's converted to base64 format
2. **AI Analysis**: Gemini Vision API analyzes the image and extracts expense information
3. **Image Storage**: Receipt images are uploaded to Supabase Storage
4. **Data Storage**: Expenses (including image URLs) are saved to Supabase database
5. **Display**: All expenses are loaded from Supabase and displayed with receipt thumbnails

## License

ISC
# 2025_2026_financial
