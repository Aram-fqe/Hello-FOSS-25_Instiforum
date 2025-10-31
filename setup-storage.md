# Supabase Storage Setup

## 1. Create Storage Bucket

In your Supabase dashboard:

1. Go to **Storage** section
2. Click **Create Bucket**
3. Name: `avatars`
4. Set as **Public bucket** (check the box)
5. Click **Create bucket**

## 2. Set Storage Policies

Go to **Storage > Policies** and create these policies for the `avatars` bucket:

### Policy 1: Allow authenticated users to upload
```sql
CREATE POLICY "Users can upload their own avatar" ON storage.objects
FOR INSERT WITH CHECK (
  bucket_id = 'avatars' 
  AND auth.uid()::text = (storage.foldername(name))[1]
);
```

### Policy 2: Allow public read access
```sql
CREATE POLICY "Avatar images are publicly accessible" ON storage.objects
FOR SELECT USING (bucket_id = 'avatars');
```

### Policy 3: Allow users to update their own avatar
```sql
CREATE POLICY "Users can update their own avatar" ON storage.objects
FOR UPDATE USING (
  bucket_id = 'avatars' 
  AND auth.uid()::text = (storage.foldername(name))[1]
);
```

## 3. Update Users Table

Add the image column to your users table:

```sql
ALTER TABLE users ADD COLUMN image TEXT;
```

## 4. File Upload Limits

The component validates:
- File types: JPEG, PNG, WebP
- Max size: 5MB
- Automatic file naming with user ID and timestamp