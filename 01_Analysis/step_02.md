**USER**
இங்கே நாம் ஜா-வாஸ்கிரிப்ட் AJAX-க்கு போகலாம். சர்வர்-பக்க ரெண்டரிங் (Server-Side Rendering) தேவை இல்லை.

**RESPONSE**
நிச்சயமாக! Server-Side Rendering (SSR) என்பதற்குப் பதிலாக, கிளையன்ட் பக்கத்தில் JavaScript மற்றும் AJAX ஐப் பயன்படுத்தி ஒரு Single Page Application (SPA) போலச் செயல்படலாம். இதன் மூலம், பக்கத்தை மீண்டும் முழுமையாக ஏற்றாமல், உள்ளடக்கத்தை மாறும் dynamic page ஆக மாற்றலாம்.

இதைச் செய்ய, நாம் சில மாற்றங்களைச் செய்ய வேண்டும்:

1.  **Handlebars Templates நீக்குதல்:** நமக்கு இனி Handlebars தேவையில்லை. நாம் HTML கோப்பிலேயே படிவத்தை உருவாக்கலாம்.
2.  **AJAX கோரிக்கை:** படிவத்தைச் சமர்ப்பிக்கும்போது, AJAX ஐப் பயன்படுத்தி Express சேவையகத்திற்கு POST கோரிக்கையை அனுப்ப வேண்டும்.
3.  **JSON பதில்:** Express சேவையகம் JSON வடிவத்தில் பதில்களை அனுப்பும்.
4.  **கிளையன்ட் பக்க JavaScript:** AJAX பதிலைப் பெற்று, பக்கத்தின் UI ஐ dynamic ஆக மாற்ற வேண்டும்.

இப்போது, தேவையான மாற்றங்களைச் செய்வோம்.

**படி 1: Handlebars மற்றும் Body Parser நீக்குதல்**

முதலில், `express-handlebars` மற்றும் `body-parser` ஐ நீக்கலாம் (அல்லது `body-parser` ஐ Express உள்ளமைக்கப்பட்ட பதிப்பைப் பயன்படுத்தலாம்).

`package.json` இல் இருந்து `express-handlebars` மற்றும் `body-parser` ஐ நீக்கிவிட்டு, `npm uninstall express-handlebars body-parser` ஐ இயக்கலாம்.

**`app.js` இல் மாற்றங்கள்:**

```javascript
// app.js

const express = require('express');
const path = require('path');

const app = express();
const PORT = process.env.PORT || 3000;

// Body Parser Middleware for JSON (Express's built-in one)
app.use(express.json()); // To parse JSON bodies
app.use(express.urlencoded({ extended: false })); // To parse URL-encoded bodies

// Static Folder (for HTML, CSS, JS files)
app.use(express.static(path.join(__dirname, 'public')));

// Routes
// We will serve our static HTML file for the root
app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, 'public', 'index.html'));
});

// API Endpoint for posting
app.post('/api/post', (req, res) => {
    const { message, image_url } = req.body;
    console.log('Received Post Data (via AJAX):');
    console.log('Message:', message);
    console.log('Image URL:', image_url);

    // --- Here's where social media integration would go ---
    // Simulate a successful post.
    const postedSuccessfully = true; // Simulate success or failure

    if (postedSuccessfully) {
        // Send a JSON response indicating success
        res.json({
            status: 'success',
            message: 'Post successfully scheduled/published!',
            data: { message, image_url }
        });
    } else {
        // Send a JSON response indicating failure
        res.status(500).json({ // Use 500 status code for server error
            status: 'error',
            message: 'Failed to publish post. Please try again.',
            data: { message, image_url }
        });
    }
});

// Start Server
app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
    console.log(`Open your browser at http://localhost:${PORT}`);
});
```

**படி 2: கிளையன்ட் பக்க HTML மற்றும் JavaScript**

இப்போது, `public` என்ற கோப்புறையை உருவாக்கி, அதற்குள் `index.html` மற்றும் `script.js` கோப்புகளை உருவாக்கவும்.

**கோப்பு அமைப்பு (File Structure):**

```
social-media-poster/
├── node_modules/
├── public/
│   ├── index.html
│   └── script.js
├── app.js
├── package.json
└── package-lock.json
```

**`public/index.html`:**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Social Media Poster</title>
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
        #status-message { margin-top: 15px; font-weight: bold; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Create New Post</h1>

        <div id="status-message"></div>

        <form id="post-form">
            <label for="message">Post Message:</label>
            <textarea id="message" name="message" rows="6" placeholder="What's on your mind?" required></textarea>

            <label for="image_url">Image URL (Optional):</label>
            <input type="text" id="image_url" name="image_url" placeholder="e.g., https://example.com/image.jpg">

            <button type="submit">Publish to Social Media</button>
        </form>

        <div id="last-posted-content" class="posted-content" style="display: none;">
            <h3>Last Posted Content:</h3>
            <p><strong>Message:</strong> <span id="posted-message-display"></span></p>
            <div id="posted-image-container" style="display: none;">
                <p><strong>Image:</strong></p>
                <img id="posted-image-display" src="" alt="Posted Image">
            </div>
        </div>
    </div>

    <script src="/script.js"></script>
</body>
</html>
```

**`public/script.js`:**

```javascript
document.addEventListener('DOMContentLoaded', () => {
    const postForm = document.getElementById('post-form');
    const messageInput = document.getElementById('message');
    const imageUrlInput = document.getElementById('image_url');
    const statusMessageDiv = document.getElementById('status-message');
    const lastPostedContentDiv = document.getElementById('last-posted-content');
    const postedMessageDisplay = document.getElementById('posted-message-display');
    const postedImageContainer = document.getElementById('posted-image-container');
    const postedImageDisplay = document.getElementById('posted-image-display');

    postForm.addEventListener('submit', async (e) => {
        e.preventDefault(); // Prevent default form submission

        const message = messageInput.value;
        const imageUrl = imageUrlInput.value;

        // Clear previous status messages
        statusMessageDiv.innerHTML = '';
        statusMessageDiv.className = '';

        try {
            const response = await fetch('/api/post', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ message, image_url: imageUrl }),
            });

            const data = await response.json();

            if (response.ok) { // Check if HTTP status code is 2xx
                statusMessageDiv.className = 'message success';
                statusMessageDiv.textContent = data.message;

                // Update last posted content
                postedMessageDisplay.textContent = data.data.message;
                if (data.data.image_url) {
                    postedImageDisplay.src = data.data.image_url;
                    postedImageContainer.style.display = 'block';
                } else {
                    postedImageContainer.style.display = 'none';
                    postedImageDisplay.src = '';
                }
                lastPostedContentDiv.style.display = 'block';

                // Optionally clear the form
                messageInput.value = '';
                imageUrlInput.value = '';

            } else {
                statusMessageDiv.className = 'message error';
                statusMessageDiv.textContent = data.message || 'An unknown error occurred.';
                lastPostedContentDiv.style.display = 'none'; // Hide last posted content on error
            }
        } catch (error) {
            console.error('Error posting:', error);
            statusMessageDiv.className = 'message error';
            statusMessageDiv.textContent = 'Network error or server unavailable. Please try again.';
            lastPostedContentDiv.style.display = 'none'; // Hide last posted content on error
        }
    });
});
```

**படி 3: சேவையகத்தை துவக்குதல் (Running the Server)**

உங்கள் டெர்மினலில், திட்டத்தின் ரூட் கோப்புறையில் இருந்து பின்வரும் கட்டளையை இயக்கவும்:

```bash
node app.js
```

உங்கள் உலாவியில் `http://localhost:3000` ஐத் திறந்து பாருங்கள். இப்போது நீங்கள் படிவத்தைச் சமர்ப்பிக்கும்போது, பக்கம் முழுமையாக மீண்டும் ஏற்றப்படாமல், AJAX மூலம் சேவையகத்துடன் தொடர்பு கொண்டு, பதில் பக்கத்தின் ஒரு பகுதியை dynamic ஆக புதுப்பிக்கும்.

**AJAX அணுகுமுறையின் நன்மைகள்:**

*   **வேகம்:** முழுப் பக்கமும் மீண்டும் ஏற்றப்பட வேண்டியதில்லை என்பதால், பயனர் இடைமுகம் வேகமாக இருக்கும்.
*   **பயனர் அனுபவம்:** seamless ஆன பயனர் அனுபவத்தை வழங்குகிறது.
*   **பிரிப்பு:** கிளையன்ட் (HTML, CSS, JS) மற்றும் சேவையக (Node.js API) தர்க்கத்தை தெளிவாகப் பிரிக்கிறது.

இந்த அமைப்பு ஒரு நவீன வலை பயன்பாடுகளின் அடிப்படைக் கட்டமைப்பை வழங்குகிறது. நீங்கள் சொன்னது போல, இது REST API கருத்துக்களையும், Node.js மற்றும் Express.js ஐயும் AJAX உடன் சிறப்பாகப் பயன்படுத்துகிறது.
