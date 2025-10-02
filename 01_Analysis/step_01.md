**USER**

ஒரு பதிவை உருவாக்கி, அதனை ஃபேஸ்புக் (Facebook), இன்ஸ்டாகிராம் (Instagram), வாட்ஸ்அப் (WhatsApp) போன்றவற்றுக்கு ஒரே நேரத்தில் பதிவிட (post) வேண்டியுள்ளது. இதற்காக, 
ஒரு வலைப் பக்க முறைமை (web page method) மூலம் Node.js, Handlebars, REST API கோட்பாடுகள் (concepts), Express.js
ஆகியவற்றைப் பயன்படுத்தி முதற்கட்டமாக ஒரு சிறு ஆய்வு/முயற்சி (aavai/mutrchi - attempt/experiment) மேற்கொள்ளலாம் என்று நினைக்கிறேன். இதனைச் செய்வோமா?

**RESPONSE**

கண்டிப்பாக, Facebook, Instagram மற்றும் WhatsApp போன்ற தளங்களில் ஒரே நேரத்தில் இடுகையிட ஒரு வலைப்பக்க அடிப்படையிலான பயன்பாட்டை உருவாக்கலாம். Node.js, Handlebars, REST API கருத்துக்கள் மற்றும் Express.js ஆகியவற்றைப் பயன்படுத்தி இதை உருவாக்குவது எப்படி என்று பார்ப்போம்.

**திட்டம் (Plan):**

1.  **Project Setup:** ஒரு புதிய Node.js திட்டத்தை உருவாக்கி, தேவையான சார்புகளை (dependencies) நிறுவ வேண்டும்.
2.  **Express.js Server:** ஒரு அடிப்படை Express.js சேவையகத்தை அமைக்க வேண்டும்.
3.  **Handlebars Templates:** இடுகையை உருவாக்க ஒரு பயனர் இடைமுகத்தை (user interface) வழங்க Handlebars ஐப் பயன்படுத்த வேண்டும்.
4.  **REST API Endpoints:** இடுகையைச் சமர்ப்பிக்க மற்றும் செயலாக்க REST API இறுதிப் புள்ளிகளை (endpoints) உருவாக்க வேண்டும்.
5.  **Social Media Integration (Dummy/Placeholder):** உண்மையான சமூக ஊடக APIகளை ஒருங்கிணைப்பதற்குப் பதிலாக, முதலில் "வெற்றிகரமாக இடுகையிடப்பட்டது" என்று ஒரு போலி (dummy) பதிலுடன் செயல்படலாம். உண்மையான ஒருங்கிணைப்புக்கு, ஒவ்வொரு தளத்தின் APIகளையும் தனித்தனியாகப் பார்க்க வேண்டும் (இது ஒரு பெரிய படி).

இப்போது, படிப்படியாக ஆரம்பிக்கலாம்.

**படி 1: Project Setup**

முதலில், ஒரு புதிய கோப்புறையை உருவாக்கி, அதில் Node.js திட்டத்தை உருவாக்கலாம்.

1.  ஒரு புதிய கோப்புறையை உருவாக்கவும்:
    ```bash
    mkdir social-media-poster
    cd social-media-poster
    ```

2.  Node.js திட்டத்தை துவக்கவும்:
    ```bash
    npm init -y
    ```

3.  தேவையான சார்புகளை நிறுவவும்:
    ```bash
    npm install express express-handlebars body-parser
    ```
    *   `express`: வலை சேவையகத்தை உருவாக்க.
    *   `express-handlebars`: Handlebars டெம்ப்ளேட் எஞ்சினை Express உடன் ஒருங்கிணைக்க.
    *   `body-parser`: POST கோரிக்கைகளிலிருந்து வரும் தரவைப் பகுப்பாய்வு செய்ய.

**படி 2: Express.js Server மற்றும் Handlebars Setup**

`app.js` என்ற பெயரில் ஒரு கோப்பை உருவாக்கி, பின்வரும் குறியீட்டைச் சேர்க்கவும்:

```javascript
// app.js

const express = require('express');
const exphbs = require('express-handlebars');
const bodyParser = require('body-parser');
const path = require('path'); // 'path' module is built-in

const app = express();
const PORT = process.env.PORT || 3000;

// Handlebars Middleware
app.engine('handlebars', exphbs.engine({
    defaultLayout: 'main', // The main layout file (e.g., views/layouts/main.handlebars)
    layoutsDir: path.join(__dirname, 'views/layouts') // Specify the layouts directory
}));
app.set('view engine', 'handlebars');
app.set('views', path.join(__dirname, 'views')); // Specify the views directory

// Body Parser Middleware
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());

// Static Folder (for CSS, JS files if any)
app.use(express.static(path.join(__dirname, 'public')));

// Routes
app.get('/', (req, res) => {
    res.render('home', { title: 'Create New Post' });
});

app.post('/post', (req, res) => {
    const { message, image_url } = req.body;
    console.log('Received Post Data:');
    console.log('Message:', message);
    console.log('Image URL:', image_url);

    // --- Here's where social media integration would go ---
    // For now, let's simulate a successful post.
    const postedSuccessfully = true; // Simulate success or failure

    if (postedSuccessfully) {
        res.render('home', {
            title: 'Create New Post',
            successMessage: 'Post successfully scheduled/published!',
            postedMessage: message,
            postedImageUrl: image_url
        });
    } else {
        res.render('home', {
            title: 'Create New Post',
            errorMessage: 'Failed to publish post. Please try again.',
            postedMessage: message,
            postedImageUrl: image_url
        });
    }
});

// Start Server
app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
    console.log(`Open your browser at http://localhost:${PORT}`);
});
```

**படி 3: Handlebars Templates**

இப்போது, Handlebars டெம்ப்ளேட்களை உருவாக்க வேண்டும். `views` என்ற கோப்புறையையும், அதற்குள் `layouts` என்ற துணைக்கோப்புறையையும் உருவாக்கவும்.

**கோப்பு அமைப்பு (File Structure):**

```
social-media-poster/
├── node_modules/
├── public/
├── views/
│   ├── layouts/
│   │   └── main.handlebars
│   └── home.handlebars
├── app.js
├── package.json
└── package-lock.json
```

**`views/layouts/main.handlebars`:** (இது எல்லா பக்கங்களுக்கும் பொதுவான தளவமைப்பு)

```handlebars
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{title}} - Social Media Poster</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; background-color: #f4f4f4; color: #333; }
        .container { max-width: 800px; margin: auto; background: #fff; padding: 20px; border-radius: 8px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        h1 { color: #0056b3; }
        form { margin-top: 20px; }
        label { display: block; margin-bottom: 5px; font-weight: bold; }
        textarea, input[type="text"] { width: 100%; padding: 10px; margin-bottom: 10px; border: 1px solid #ddd; border-radius: 4px; box-sizing: border-box; }
        button { background-color: #007bff; color: white; padding: 10px 15px; border: none; border-radius: 4px; cursor: pointer; font-size: 16px; }
        button:hover { background-color: #0056b3; }
        .message.success { background-color: #d4edda; color: #155724; border: 1px solid #c3e6cb; padding: 10px; margin-top: 15px; border-radius: 4px; }
        .message.error { background-color: #f8d7da; color: #721c24; border: 1px solid #f5c6cb; padding: 10px; margin-top: 15px; border-radius: 4px; }
        .posted-content { margin-top: 20px; padding: 15px; border: 1px dashed #ccc; border-radius: 5px; background-color: #e9ecef; }
        .posted-content p { margin: 0 0 10px 0; }
        .posted-content img { max-width: 100%; height: auto; border-radius: 5px; margin-top: 10px; }
    </style>
</head>
<body>
    <div class="container">
        {{{body}}}
    </div>
</body>
</html>
```

**`views/home.handlebars`:** (இடுகையை உருவாக்கப் பயன்படும் பக்கம்)

```handlebars
<h1>{{title}}</h1>

{{#if successMessage}}
    <div class="message success">{{successMessage}}</div>
{{/if}}

{{#if errorMessage}}
    <div class="message error">{{errorMessage}}</div>
{{/if}}

<form action="/post" method="POST">
    <label for="message">Post Message:</label>
    <textarea id="message" name="message" rows="6" placeholder="What's on your mind?" required>{{postedMessage}}</textarea>

    <label for="image_url">Image URL (Optional):</label>
    <input type="text" id="image_url" name="image_url" placeholder="e.g., https://example.com/image.jpg" value="{{postedImageUrl}}">

    <button type="submit">Publish to Social Media</button>
</form>

{{#if postedMessage}}
    <div class="posted-content">
        <h3>Last Posted Content:</h3>
        <p><strong>Message:</strong> {{postedMessage}}</p>
        {{#if postedImageUrl}}
            <p><strong>Image:</strong></p>
            <img src="{{postedImageUrl}}" alt="Posted Image">
        {{/if}}
    </div>
{{/if}}
```

**படி 4: சேவையகத்தை துவக்குதல் (Running the Server)**

இப்போது, உங்கள் Node.js சேவையகத்தை இயக்கலாம். உங்கள் டெர்மினலில், திட்டத்தின் ரூட் கோப்புறையில் இருந்து பின்வரும் கட்டளையை இயக்கவும்:

```bash
node app.js
```

சேவையகம் வெற்றிகரமாக இயங்கினால், `Server running on port 3000` மற்றும் `Open your browser at http://localhost:3000` போன்ற செய்தியைப் பார்க்க வேண்டும்.

உங்கள் உலாவியில் `http://localhost:3000` ஐத் திறந்து பாருங்கள். ஒரு போஸ்ட் உருவாக்கும் படிவத்தைப் பார்ப்பீர்கள்.

**அடுத்த படிகள் (Next Steps) மற்றும் உண்மையான ஒருங்கிணைப்பு:**

இந்த பயன்பாடு ஒரு அடிப்படை கட்டமைப்பு மட்டுமே. உண்மையான சமூக ஊடக ஒருங்கிணைப்புக்கு பின்வரும் படிகள் தேவைப்படும்:

1.  **சமூக ஊடக APIகள் (Social Media APIs):**
    *   **Facebook Graph API:** இடுகையிட, பக்கங்களைப் பயன்படுத்த வேண்டும் மற்றும் Access Tokens தேவைப்படும்.
    *   **Instagram Graph API:** Facebook Graph API உடன் ஒருங்கிணைக்கப்பட்டுள்ளது, Business Accounts தேவை.
    *   **WhatsApp Business API:** இது மிகவும் சிக்கலானது மற்றும் வணிக ஒப்புதல் தேவை. பெரும்பாலான சிறிய பயன்பாடுகளுக்கு இது பொருத்தமற்றது. பொதுவாக, WhatsApp க்கு நேரடி API இல்லை, ஆனால் பல மூன்றாம் தரப்பு சேவை வழங்குநர்கள் உள்ளனர்.

2.  **அங்கீகாரம் (Authentication):** ஒவ்வொரு தளத்திற்கும் OAuth 2.0 போன்ற அங்கீகார ஓட்டத்தை (authentication flow) செயல்படுத்த வேண்டும், இதன் மூலம் பயனர்கள் தங்கள் சமூக ஊடக கணக்குகளை உங்கள் பயன்பாட்டுடன் இணைக்க முடியும். இது மிகவும் முக்கியமான மற்றும் சிக்கலான பகுதி.

3.  **பிழை கையாளுதல் (Error Handling):** சமூக ஊடக API அழைப்புகள் தோல்வியுற்றால் பிழைகளைச் சரியாகக் கையாள வேண்டும்.

4.  **படம் பதிவேற்றம் (Image Upload):** இப்போது URL வழியாகப் படத்தை இடுகையிடுகிறோம். ஒரு படிவம் வழியாகப் படங்களைப் பதிவேற்றும் செயல்பாடு தேவைப்படலாம், பின்னர் அதைப் பல சமூக ஊடக தளங்களுக்கு அனுப்பலாம். இதற்குக் `multer` போன்ற நூலகங்கள் உதவும்.

5.  **பயனர் கணக்குகள் (User Accounts):** பல பயனர்கள் தங்கள் சொந்த சமூக ஊடக கணக்குகளுடன் இடுகையிட வேண்டுமானால், ஒரு பயனர் கணக்கு அமைப்பு மற்றும் ஒவ்வொரு பயனருக்கும் தனிப்பட்ட டோக்கன்களைச் சேமிக்க ஒரு தரவுத்தளம் (MongoDB, PostgreSQL) தேவைப்படும்.

6.  **பின்னணி பணிகள் (Background Jobs):** சமூக ஊடக இடுகைகள் நீண்ட நேரம் எடுக்கலாம் அல்லது தோல்வியடையலாம். `Celery` (Python) அல்லது `bull` (Node.js) போன்ற பின்னணி பணி வரிசைகளைப் (background job queues) பயன்படுத்துவது நல்லது, இதனால் இடுகையிடும் செயல்முறை பின்னணியில் நடைபெறுகிறது மற்றும் பயனர் இடைமுகம் தடுக்கப்படாது.

இது ஒரு நல்ல ஆரம்பம்! இதை மேலும் விரிவாக்கலாம்.

உங்களுக்கு இந்த அமைப்பிலிருந்து ஒரு ஸ்கிரீன்ஷாட் வேண்டுமா? 
