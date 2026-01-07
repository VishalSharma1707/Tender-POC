# 📘 Tender POC (Like NoteBook LM) – Full Stack App

A **Tender POC (Like NoteBook LM)-style research assistant** built with **React (Frontend)** + **Node.js/Express (Backend)** + **MongoDB (Database)**. This application allows users to upload sources, chat with documents, save notes, and manage research notebooks.

---

## 📜 Table of Contents

- [🚀 Features](#-features)
- [🖼 UI Mapping](#-ui-mapping-react-components--db-collections)
- [🗄 MongoDB Schema](#-mongodb-schema)
- [🤝 Collaboration](#-collaboration)
- [🎨 UI/UX Notes](#-uiux-notes)
- [🛠 Tech Stack](#-tech-stack)
- [⚙️ Setup & Installation](#️-setup--installation)
- [📞 API Endpoints](#-api-endpoints-backend)
- [🚀 Future Enhancements](#-future-enhancements)
- [📜 License](#-license)
- [📞 Contact](#-contact)

---

## 🚀 Features

- 🔐 **Authentication**
  - Login with email & password
  - Social login (Google, Microsoft – placeholder UI)

- 📚 **Notebook Management**
  - Create, Rename, Delete notebooks
  - Gradient-based notebook icons
  - Collaborators with editor/viewer roles

- 📄 **Sources**
  - Upload PDFs, DOCX, URLs, or plain text
  - Extracted text content stored for AI use
  - Metadata (pages, author, published date)

- 📝 **Notes**
  - Save AI responses as Notes
  - Rich text/HTML/Markdown support
  - Tagging + Source references

- 💬 **Chat Panel**
  - User ↔ AI messaging
  - AI answers include source references
  - Copy, Save Note, Generate Audio actions

- 📊 **Activity Logs**
  - Track notebook creation, renaming, deletions
  - Future expansion for team collaboration

---

## 🖼 UI Mapping (React Components ↔ DB Collections)

- **Login.jsx** → `Users`
- **Home.jsx (Notebook Cards)** → `Notebooks`
- **ChatPanel.jsx**
  - Messages → `Chats.messages`
  - Save Note → `Notes`
  - Source reference (📄) → `Sources`
- **Notebook Options (Rename/Delete)** → `Notebooks`
- **Activity History (Optional)** → `Activity`

---

## 🗄 MongoDB Schema

### 1. Users
*Your login page has email + password → we need users table.*
```json
{
  "_id": "ObjectId",
  "name": "String",
  "email": { "type": "String", "unique": true },
  "passwordHash": "String",
  "avatar": "String",
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

### 2. Notebooks
*Each card shows: name, description, gradient icon, counts (sources, notes), last updated*
```json
{
  "_id": "ObjectId",
  "name": "String",
  "description": "String",
  "ownerId": "ObjectId",
  "gradient": "String",
  "sourcesCount": "Number",
  "notesCount": "Number",
  "updatedAt": "Date",
  "createdAt": "Date",
  "collaborators": [
    {
      "userId": "ObjectId",
      "role": { "type": "String", "enum": ["editor", "viewer"], "default": "viewer" }
    }
  ]
}
```

### 3. Sources
*Your HTML mentions "📄 Source: Research Paper.pdf (pages 3-7)" → so we need files + metadata.*
```json
{
  "_id": "ObjectId",
  "notebookId": "ObjectId",
  "title": "String",
  "type": { "type": "String", "enum": ["pdf", "url", "text", "docx"] },
  "fileUrl": "String",
  "textContent": "String",
  "metadata": {
    "pages": "Number",
    "author": "String",
    "publishedDate": "Date"
  },
  "createdBy": "ObjectId",
  "createdAt": "Date"
}
```

### 4. Notes
*From your Chat Panel, users can Save Note.*
```json
{
  "_id": "ObjectId",
  "notebookId": "ObjectId",
  "authorId": "ObjectId",
  "title": "String",
  "content": "String",
  "sourceRefs": ["ObjectId"],
  "tags": ["String"],
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

### 5. Chats
*Your ChatPanel has AI/User messages with: author (AI or User), HTML content (dangerouslySetInnerHTML), Copy/Save/Audio actions*
```json
{
  "_id": "ObjectId",
  "notebookId": "ObjectId",
  "userId": "ObjectId",
  "messages": [
    {
      "author": { "type": "String", "enum": ["user", "ai"] },
      "content": "String",
      "sourceRefs": ["ObjectId"],
      "timestamp": "Date"
    }
  ],
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

### 6. Activity / Audit (Optional)
*For your notebook options (Rename/Delete) → keep history.*
```json
{
  "_id": "ObjectId",
  "notebookId": "ObjectId",
  "userId": "ObjectId",
  "action": "String",
  "targetId": "ObjectId",
  "timestamp": "Date"
}
```

---

## 🤝 Collaboration

- **Owner**: Full control (rename, delete, add collaborators)
- **Editor**: Can add sources, notes, and chat
- **Viewer**: Read-only access to sources, notes, and chat history

---

## 🎨 UI/UX Notes

- **Gradients**: Use Tailwind CSS gradients for notebook cards.
- **Icons**: Use a library like `react-icons` for UI elements.
- **Modals**: Separate components for modals (e.g., `RenameNotebookModal.jsx`).
- **State Management**: Use React Context or a library like Zustand for managing global state (e.g., current user, selected notebook).

---

## 🛠 Tech Stack

- **Frontend**: React, Vite, Tailwind CSS
- **Backend**: Node.js, Express
- **Database**: MongoDB
- **Authentication**: JWT (JSON Web Tokens)
- **AI Integration**: Placeholder for Gemini/OpenAI API

---

## ⚙️ Setup & Installation

1. **Clone the repository:**
   ```bash
   git clone <repository-url>
   cd <project-directory>
   ```

2. **Install dependencies:**
   ```bash
   # For frontend
   cd frontend
   npm install

   # For backend
   cd ../backend
   npm install
   ```

3. **Configure environment variables:**
   - Create a `.env` file in the `backend` directory.
   - Add the following variables:
     ```
     MONGO_URI=<your-mongodb-connection-string>
     JWT_SECRET=<your-jwt-secret>
     ```

4. **Run the application:**
   ```bash
   # Run frontend (in /frontend)
   npm run dev

   # Run backend (in /backend)
   npm start
   ```

---

## 📞 API Endpoints (Backend)

- **Auth**
  - `POST /api/auth/register`
  - `POST /api/auth/login`
- **Notebooks**
  - `GET /api/notebooks`
  - `POST /api/notebooks`
  - `PUT /api/notebooks/:id`
  - `DELETE /api/notebooks/:id`
- **Sources**
  - `POST /api/notebooks/:notebookId/sources`
- **Notes**
  - `POST /api/notebooks/:notebookId/notes`

---

## 🚀 Future Enhancements

- [ ] Real-time collaboration using WebSockets
- [ ] Advanced search within notes and sources
- [ ] Integration with Google Drive, Dropbox for importing sources
- [ ] Export notes to PDF, Markdown
- [ ] User profile and settings page
