---
name: ""
overview: ""
todos: []
isProject: false
---

# Onboarding: User & Store Pictures (Mandatory)

## Overview

Add two required image uploads to supplier onboarding: user picture and store picture. Images are uploaded to a dedicated Supabase bucket and stored on the `users` row. Onboarding cannot complete without both.

## Steps

1. DB columns for image URLs

- File: `backend/add_user_pictures_to_users.sql` (already created)  
- Columns: `user_picture_url`, `store_picture_url` (nullable; onboarding will set).  
- Run migration in your DB.

1. Storage bucket

- Create a new Supabase storage bucket, e.g., `user_media`, to keep onboarding photos separate from product images.  
- Public read is acceptable if consistent with your product images; otherwise use signed URLs (not implemented here).

1. Frontend: onboarding form updates

- File: `frontend/src/app/onboarding/page.tsx`  
- Add state for `userPicture` and `storePicture`, preview UI, and file inputs (step 3).  
- Validation: both files required; block submit if missing.  
- Upload both images to `user_media` before saving; generate unique path `${userId}/user-...` and `${userId}/store-...`, then call `getPublicUrl`.  
- On submit, include `user_picture_url` and `store_picture_url` in the `users` update; keep `onboarded: true` logic.  
- Show friendly error if upload fails.

1. (Optional) Local storage / dashboard

- If you surface profile data elsewhere, consider storing the URLs in localStorage alongside other supplier info, or fetch from DB where needed.

## Testing

- Onboarding blocks submission when either image is missing.  
- Successful submission uploads both images, saves URLs, and completes onboarding.

