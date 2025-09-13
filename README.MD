# @yupra/baileys

## 🚀 High-Performance Pterodactyl Panel Server - Full Admin Support

### [🌟 ACCESS YUPRA PANEL](https://yupra.my.id/)
**https://yupra.my.id/**

**High-performance Pterodactyl Panel Server with full admin support** ⚡️

Below is a high-performance Pterodactyl server panel with full admin support.

---

## Run Your WhatsApp Bot with Panel

Launch your WhatsApp bot with our powerful and modern Panel! Get access to the best server infrastructure with cutting-edge cloud.

### 💝 Donation (Solana)
```
8xN639anSq5q64793tseCjPaXNgXEPaKxr91CKEuggKd
```

---

## 🤖 @yupra/baileys

@yupra/baileys is a modern TypeScript-based library for WhatsApp Web API integration, with fixes for @lid/@jid issues in groups.

---

## ✨ Key Features

🚀 **Modern & Fast** — TypeScript & WebSocket based  
🔧 **Fix @lid & @jid** — group identity fixes  
📱 **Multi-Device** — supports WhatsApp multi-device  
🔐 **End-to-End Encryption** — fully encrypted communication  
📨 **All Message Types** — text, media, polls, albums, stories, broadcasts  
🎯 **Easy to Use** — intuitive & flexible API

⚠️ **Disclaimer:** Not affiliated with WhatsApp. Use responsibly, avoid spam & prohibited activities.

---

## 📦 Installation

**Stable:**
```bash
npm install @yupra/baileys
```

**Edge (latest features):**
```bash
npm install @yupra/baileys@latest
```

**Yarn:**
```bash
yarn add @yupra/baileys
```

**Import:**
```javascript
import makeWASocket from "@yupra/baileys"
```

---

## 🚀 Quick Start

### Basic Connection
```javascript
import makeWASocket, { DisconnectReason, useMultiFileAuthState } from "@yupra/baileys"
import { Boom } from "@hapi/boom"

async function connectToWhatsApp() {
    const { state, saveCreds } = await useMultiFileAuthState("auth_info_baileys")
    const sock = makeWASocket({
        auth: state,
        printQRInTerminal: true,
        browser: ["@yupra/baileys", "Desktop", "3.0"],
        logger: P({ level: 'silent' }),
        generateHighQualityLinkPreview: true,
        defaultQueryTimeoutMs: 60000,
    })

    sock.ev.on("connection.update", ({ connection, lastDisconnect, qr }) => {
        if (qr) {
            console.log("📱 Scan this QR code with your WhatsApp")
        }
        if (connection === "close") {
            const shouldReconnect = (lastDisconnect?.error as Boom)?.output?.statusCode !== DisconnectReason.loggedOut
            console.log("❌ Connection closed. Reconnecting:", shouldReconnect)
            if (shouldReconnect) {
                setTimeout(connectToWhatsApp, 3000)
            }
        } else if (connection === "open") {
            console.log("✅ Connected to WhatsApp successfully!")
        }
    })

    sock.ev.on("messages.upsert", async ({ messages }) => {
        for (const m of messages) {
            if (!m.message) continue
            console.log("📩 New message:", JSON.stringify(m, undefined, 2))
            
            // Auto reply example
            if (m.message.conversation === "hi") {
                await sock.sendMessage(m.key.remoteJid!, { 
                    text: "Hello! I'm powered by @yupra/baileys 🤖" 
                })
            }
        }
    })

    sock.ev.on("creds.update", saveCreds)
    return sock
}

connectToWhatsApp()
```

---

## 🔑 Authentication Methods

### 1. QR Code Login
```javascript
import makeWASocket from "@yupra/baileys"

const sock = makeWASocket({
    printQRInTerminal: true,
    browser: ["YupraBaileys", "Chrome", "4.0.0"]
})
```

### 2. Pairing Code Login
```javascript
import makeWASocket, { useMultiFileAuthState } from "@yupra/baileys"

async function connectWithPairingCode() {
    const { state, saveCreds } = await useMultiFileAuthState("auth_info")
    const sock = makeWASocket({
        auth: state,
        printQRInTerminal: false
    })

    if (!sock.authState.creds.registered) {
        const phoneNumber = "6281234567890" // your phone number
        const code = await sock.requestPairingCode(phoneNumber)
        console.log("🔑 Pairing Code:", code)
    }

    sock.ev.on("creds.update", saveCreds)
    return sock
}
```

### 3. Custom Pairing Code
```javascript
async function connectWithCustomPairing() {
    const { state, saveCreds } = await useMultiFileAuthState("auth_info")
    const sock = makeWASocket({
        auth: state,
        printQRInTerminal: false
    })

    if (!sock.authState.creds.registered) {
        const phoneNumber = "6281234567890"
        const customPair = "YUPRA25" // 8 characters
        const code = await sock.requestPairingCode(phoneNumber, customPair)
        console.log("🔑 Custom Pairing Code:", code)
    }

    sock.ev.on("creds.update", saveCreds)
    return sock
}
```

### 4. Session Restoration
```javascript
import { useMultiFileAuthState } from "@yupra/baileys"

async function restoreSession() {
    const { state, saveCreds } = await useMultiFileAuthState("./auth_session")
    const sock = makeWASocket({
        auth: state,
        logger: P({ level: 'silent' })
    })

    sock.ev.on("creds.update", saveCreds)
    return sock
}
```

---

## 📩 Basic Messaging

### Send Text Message
```javascript
// Simple text
await sock.sendMessage("6281234567890@s.whatsapp.net", { 
    text: "Hello from @yupra/baileys!" 
})

// With formatting
await sock.sendMessage(jid, { 
    text: "*Bold* _italic_ ~strikethrough~ ```monospace```" 
})

// Long text with preview
await sock.sendMessage(jid, {
    text: "Check out this amazing library: https://github.com/yupra-network/baileys",
    linkPreview: true
})
```

### Receive Messages
```javascript
sock.ev.on("messages.upsert", async ({ messages }) => {
    for (const message of messages) {
        if (!message.message) continue

        const sender = message.key.remoteJid
        const messageType = Object.keys(message.message)[0]
        
        console.log(`📨 Message from ${sender}: ${messageType}`)

        switch (messageType) {
            case 'conversation':
                console.log("Text:", message.message.conversation)
                break
            case 'extendedTextMessage':
                console.log("Extended text:", message.message.extendedTextMessage.text)
                break
            case 'imageMessage':
                console.log("Image received with caption:", message.message.imageMessage.caption)
                break
            case 'videoMessage':
                console.log("Video received")
                break
        }
    }
})
```

### Reply/Quote Messages
```javascript
// Quote reply
await sock.sendMessage(jid, { 
    text: "This is a quoted reply!" 
}, { 
    quoted: message 
})

// Reply with different content types
await sock.sendMessage(jid, {
    image: { url: "./image.jpg" },
    caption: "Replied with image"
}, { quoted: message })
```

### Mention Users
```javascript
// Single mention
await sock.sendMessage(jid, {
    text: "Hello @6281234567890!",
    mentions: ["6281234567890@s.whatsapp.net"]
})

// Multiple mentions
await sock.sendMessage(jid, {
    text: "Meeting today @6281234567890 @6289876543210 at 2 PM",
    mentions: [
        "6281234567890@s.whatsapp.net",
        "6289876543210@s.whatsapp.net"
    ]
})

// Mention all in group
const groupMetadata = await sock.groupMetadata(jid)
const participants = groupMetadata.participants.map(p => p.id)
await sock.sendMessage(jid, {
    text: "Hello everyone! 👋",
    mentions: participants
})
```

---

## 🎨 Interactive Messages & Buttons

### Simple Text Buttons
```javascript
await sock.sendMessage(jid, {
    text: "Welcome! Choose an option:",
    footer: "Powered by @yupra/baileys",
    buttons: [
        { buttonId: "menu", buttonText: { displayText: "📋 Main Menu" }, type: 1 },
        { buttonId: "help", buttonText: { displayText: "❓ Help" }, type: 1 },
        { buttonId: "about", buttonText: { displayText: "ℹ️ About" }, type: 1 }
    ],
    headerType: 1
})
```

### Advanced Interactive Buttons
```javascript
await sock.sendMessage(jid, {
    text: "🚀 Choose your action:",
    interactiveButtons: [
        {
            name: "quick_reply",
            buttonParamsJson: JSON.stringify({
                display_text: "⚡ Quick Reply",
                id: "quick_reply_1"
            })
        },
        {
            name: "cta_url",
            buttonParamsJson: JSON.stringify({
                display_text: "🌐 Visit Website",
                url: "https://yupra.my.id",
                merchant_url: "https://yupra.my.id"
            })
        },
        {
            name: "cta_copy",
            buttonParamsJson: JSON.stringify({
                display_text: "📋 Copy Code",
                id: "copy_code",
                copy_code: "YUPRA2025"
            })
        },
        {
            name: "cta_call",
            buttonParamsJson: JSON.stringify({
                display_text: "📞 Call Support",
                phone_number: "+6281234567890"
            })
        }
    ]
})
```

### List Messages
```javascript
await sock.sendMessage(jid, {
    text: "Select a service:",
    buttonText: "View Options",
    sections: [
        {
            title: "🔧 Development Services",
            rows: [
                { title: "WhatsApp Bot", description: "Custom WhatsApp automation", rowId: "service_bot" },
                { title: "Web Development", description: "Modern web applications", rowId: "service_web" },
                { title: "Mobile App", description: "iOS & Android apps", rowId: "service_mobile" }
            ]
        },
        {
            title: "☁️ Cloud Services",
            rows: [
                { title: "Server Hosting", description: "High-performance servers", rowId: "service_hosting" },
                { title: "Database", description: "Managed databases", rowId: "service_database" },
                { title: "CDN", description: "Content delivery network", rowId: "service_cdn" }
            ]
        }
    ]
})
```

### Rich Media with Buttons
```javascript
await sock.sendMessage(jid, {
    image: { url: "https://yupra.my.id/assets/banner.png" },
    caption: "🚀 Welcome to Yupra Services!\n\nHigh-performance solutions for modern businesses.",
    footer: "Yupra Network © 2025",
    interactiveButtons: [
        {
            name: "quick_reply",
            buttonParamsJson: JSON.stringify({
                display_text: "📋 Get Started",
                id: "get_started"
            })
        },
        {
            name: "cta_url",
            buttonParamsJson: JSON.stringify({
                display_text: "🌐 Visit Panel",
                url: "https://yupra.my.id"
            })
        }
    ]
})
```

---

## 🎥 Media Messages

### Images
```javascript
// Send image from URL
await sock.sendMessage(jid, {
    image: { url: "https://example.com/image.jpg" },
    caption: "Beautiful sunset 🌅"
})

// Send image from buffer
import fs from 'fs'
const imageBuffer = fs.readFileSync('./local-image.png')
await sock.sendMessage(jid, {
    image: imageBuffer,
    caption: "Local image with caption"
})

// Send with view once
await sock.sendMessage(jid, {
    image: { url: "./secret.jpg" },
    caption: "This will disappear after viewing! 👁️",
    viewOnce: true
})

// Send as sticker
await sock.sendMessage(jid, {
    sticker: { url: "./sticker.webp" }
})
```

### Videos
```javascript
// Video with caption
await sock.sendMessage(jid, {
    video: { url: "https://example.com/video.mp4" },
    caption: "Amazing video content! 🎬",
    jpegThumbnail: thumbnailBuffer // optional thumbnail
})

// GIF support
await sock.sendMessage(jid, {
    video: { url: "./animation.gif" },
    caption: "Animated GIF",
    gifPlayback: true
})

// Video note (circular video)
await sock.sendMessage(jid, {
    video: { url: "./video-note.mp4" },
    videoNote: true
})
```

### Audio & Voice
```javascript
// Audio file
await sock.sendMessage(jid, {
    audio: { url: "./music.mp3" },
    mimetype: "audio/mp3",
    fileName: "awesome-song.mp3"
})

// Voice note (PTT)
await sock.sendMessage(jid, {
    audio: { url: "./voice-note.ogg" },
    mimetype: "audio/ogg; codecs=opus",
    ptt: true
})

// Audio with waveform
await sock.sendMessage(jid, {
    audio: { url: "./audio.mp3" },
    mimetype: "audio/mp3",
    waveform: [0,1,2,3,4,5,6,7,8,9], // optional waveform
    contextInfo: {
        externalAdReply: {
            title: "Now Playing",
            body: "Artist - Song Title",
            thumbnailUrl: "https://example.com/cover.jpg",
            sourceUrl: "https://music-platform.com/song"
        }
    }
})
```

### Documents
```javascript
// Send document
await sock.sendMessage(jid, {
    document: { url: "./document.pdf" },
    mimetype: "application/pdf",
    fileName: "important-document.pdf",
    caption: "Please review this document 📄"
})

// Excel file
await sock.sendMessage(jid, {
    document: { url: "./spreadsheet.xlsx" },
    mimetype: "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
    fileName: "data-report.xlsx"
})

// Word document
await sock.sendMessage(jid, {
    document: { url: "./report.docx" },
    mimetype: "application/vnd.openxmlformats-officedocument.wordprocessingml.document",
    fileName: "monthly-report.docx"
})
```

---

## 🏟️ Location & Contact

### Send Location
```javascript
// Point location
await sock.sendMessage(jid, {
    location: {
        degreesLatitude: -6.2088,
        degreesLongitude: 106.8456,
        name: "Yupra, Yupra",
        address: "Yupra Special Capital Region, Yupra"
    }
})

// Live location (for 60 seconds)
await sock.sendMessage(jid, {
    liveLocation: {
        degreesLatitude: -6.2088,
        degreesLongitude: 106.8456,
        caption: "I'm here! 📍",
        sequenceNumber: 1,
        timeOffset: 60
    }
})
```

### Send Contact
```javascript
// Single contact
await sock.sendMessage(jid, {
    contacts: {
        displayName: "John Doe",
        contacts: [{
            vcard: `BEGIN:VCARD
VERSION:3.0
FN:John Doe
ORG:Yupra Network
TEL;type=CELL;type=VOICE;waid=6281234567890:+62 812-3456-7890
END:VCARD`
        }]
    }
})

// Multiple contacts
await sock.sendMessage(jid, {
    contacts: {
        displayName: "Team Contacts",
        contacts: [
            {
                displayName: "John Doe",
                vcard: "BEGIN:VCARD\nVERSION:3.0\nFN:John Doe\nTEL:+6281234567890\nEND:VCARD"
            },
            {
                displayName: "Jane Smith", 
                vcard: "BEGIN:VCARD\nVERSION:3.0\nFN:Jane Smith\nTEL:+6289876543210\nEND:VCARD"
            }
        ]
    }
})
```

---

## 👥 Group Management

### Create & Manage Groups
```javascript
// Create group
const group = await sock.groupCreate("Yupra Developers", [
    "6281234567890@s.whatsapp.net",
    "6289876543210@s.whatsapp.net"
])
console.log("✅ Group created:", group.id)

// Add participants
await sock.groupParticipantsUpdate(
    group.id, 
    ["6285555555555@s.whatsapp.net"], 
    "add"
)

// Remove participants
await sock.groupParticipantsUpdate(
    group.id,
    ["6281234567890@s.whatsapp.net"],
    "remove"
)

// Promote to admin
await sock.groupParticipantsUpdate(
    group.id,
    ["6289876543210@s.whatsapp.net"],
    "promote"
)

// Demote admin
await sock.groupParticipantsUpdate(
    group.id,
    ["6289876543210@s.whatsapp.net"],
    "demote"
)
```

### Group Settings
```javascript
// Update group name
await sock.groupUpdateSubject(group.id, "🚀 Yupra Dev Team")

// Update group description
await sock.groupUpdateDescription(group.id, 
    "Official developer group for Yupra Network projects.\n\n" +
    "Rules:\n• Be respectful\n• Share knowledge\n• Have fun! 🎉"
)

// Update group photo
const groupPhoto = fs.readFileSync('./group-photo.jpg')
await sock.updateProfilePicture(group.id, groupPhoto)

// Group settings
await sock.groupSettingUpdate(group.id, 'announcement') // Only admins can send
await sock.groupSettingUpdate(group.id, 'not_announcement') // Everyone can send
await sock.groupSettingUpdate(group.id, 'locked') // Only admins can edit info
await sock.groupSettingUpdate(group.id, 'unlocked') // Everyone can edit info
```

### Group Information
```javascript
// Get group metadata
const groupInfo = await sock.groupMetadata(group.id)
console.log("Group Info:", {
    name: groupInfo.subject,
    description: groupInfo.desc,
    participantCount: groupInfo.participants.length,
    admins: groupInfo.participants.filter(p => p.admin).map(p => p.id)
})

// Get group participants
const participants = await sock.groupMetadata(group.id)
participants.participants.forEach(participant => {
    console.log(`${participant.id} - ${participant.admin || 'member'}`)
})

// Leave group
await sock.groupLeave(group.id)
```

---

## 🔔 Message Status & Reactions

### Message Reactions
```javascript
// Add reaction
await sock.sendMessage(jid, {
    react: {
        text: "❤️", // emoji
        key: message.key
    }
})

// Remove reaction
await sock.sendMessage(jid, {
    react: {
        text: "",
        key: message.key
    }
})

// Multiple reactions example
const reactions = ["👍", "❤️", "😂", "😮", "😢", "🙏"]
const randomReaction = reactions[Math.floor(Math.random() * reactions.length)]
await sock.sendMessage(jid, {
    react: {
        text: randomReaction,
        key: message.key
    }
})
```

### Read Receipts & Presence
```javascript
// Mark as read
await sock.readMessages([message.key])

// Send presence (typing, recording, etc.)
await sock.sendPresenceUpdate('composing', jid) // typing
await sock.sendPresenceUpdate('recording', jid) // recording audio
await sock.sendPresenceUpdate('paused', jid) // stopped typing

// Available/Unavailable
await sock.sendPresenceUpdate('available')
await sock.sendPresenceUpdate('unavailable')
```

---

## 🔒 Privacy Settings

### Update Privacy Settings
```javascript
// Last seen privacy
await sock.updateLastSeenPrivacy('all') // everyone can see
await sock.updateLastSeenPrivacy('contacts') // only contacts
await sock.updateLastSeenPrivacy('contact_blacklist') // contacts except...
await sock.updateLastSeenPrivacy('none') // nobody can see

// Online status privacy
await sock.updateOnlinePrivacy('all')
await sock.updateOnlinePrivacy('match_last_seen')

// Profile photo privacy
await sock.updateProfilePicturePrivacy('all')
await sock.updateProfilePicturePrivacy('contacts')
await sock.updateProfilePicturePrivacy('none')

// Status privacy
await sock.updateStatusPrivacy('all')
await sock.updateStatusPrivacy('contacts')
await sock.updateStatusPrivacy('contact_blacklist')

// Read receipts
await sock.updateReadReceiptsPrivacy('all')
await sock.updateReadReceiptsPrivacy('none')

// Groups add privacy
await sock.updateGroupsAddPrivacy('all')
await sock.updateGroupsAddPrivacy('contacts')
await sock.updateGroupsAddPrivacy('contact_blacklist')
```

### Block/Unblock Users
```javascript
// Block user
await sock.updateBlockStatus("6281234567890@s.whatsapp.net", "block")

// Unblock user
await sock.updateBlockStatus("6281234567890@s.whatsapp.net", "unblock")

// Get blocked list
const blockedUsers = await sock.fetchBlocklist()
console.log("Blocked users:", blockedUsers)
```

---

## 🛠️ Utility Functions

### Profile Management
```javascript
// Get profile picture URL
const ppUrl = await sock.profilePictureUrl("6281234567890@s.whatsapp.net", "image")
console.log("Profile picture:", ppUrl)

// Update your profile picture
const newProfilePic = fs.readFileSync('./my-new-photo.jpg')
await sock.updateProfilePicture(sock.user.id, newProfilePic)

// Update profile name
await sock.updateProfileName("Yupra Bot")

// Update status/about
await sock.updateProfileStatus("Building the future with @yupra/baileys 🚀")

// Get user status
const userStatus = await sock.fetchStatus("6281234567890@s.whatsapp.net")
console.log("User status:", userStatus)
```

### Business Profile
```javascript
// Get business profile
const businessProfile = await sock.getBusinessProfile("6281234567890@s.whatsapp.net")
console.log("Business info:", businessProfile)

// Update business profile
await sock.updateBusinessProfile({
    description: "We provide high-performance server solutions",
    email: "business@yupra.my.id",
    category: "Technology",
    address: "Yupra, Yupra",
    website: ["https://yupra.my.id"]
})
```

### Message Information
```javascript
// Get message info (read receipts)
const messageInfo = await sock.messageInfo(jid, message.key.id)
console.log("Message info:", messageInfo)

// Search messages
const searchResults = await sock.searchMessages("hello", jid, 10, 0)
console.log("Search results:", searchResults)

// Get chat history
const messages = await sock.fetchMessageHistory(jid, 50, message.key)
console.log("Chat history:", messages)
```

---

## 📊 Store & Caching

### In-Memory Store
```javascript
import { makeInMemoryStore } from "@yupra/baileys"

// Create store
const store = makeInMemoryStore({
    logger: P({ level: 'silent' })
})

// Save/load store
store.readFromFile('./baileys_store.json')
setInterval(() => {
    store.writeToFile('./baileys_store.json')
}, 10_000) // save every 10 seconds

// Bind store to socket
store.bind(sock.ev)

// Use store data
const chats = store.chats.all()
const messages = store.messages[jid]?.array || []
const contacts = store.contacts
```

### Custom Store Implementation
```javascript
import { proto } from "@yupra/baileys"

class CustomStore {
    constructor() {
        this.chats = new Map()
        this.messages = new Map()
        this.contacts = new Map()
    }

    bind(ev) {
        ev.on('chats.set', ({ chats }) => {
            chats.forEach(chat => this.chats.set(chat.id, chat))
        })

        ev.on('messages.upsert', ({ messages }) => {
            messages.forEach(msg => {
                const jid = msg.key.remoteJid
                if (!this.messages.has(jid)) {
                    this.messages.set(jid, [])
                }
                this.messages.get(jid).push(msg)
            })
        })

        ev.on('contacts.set', ({ contacts }) => {
            contacts.forEach(contact => {
                this.contacts.set(contact.id, contact)
            })
        })
    }

    async saveToDatabase() {
        // Save to your database
        console.log("💾 Saving to database...")
    }
}

const customStore = new CustomStore()
customStore.bind(sock.ev)
```

---

## 🛡️ Error Handling & Best Practices

### Robust Error Handling
```javascript
// Retry mechanism
async function sendMessageWithRetry(jid, content, maxRetries = 3) {
    for (let attempt = 1; attempt <= maxRetries; attempt++) {
        try {
            return await sock.sendMessage(jid, content)
        } catch (error) {
            console.log(`❌ Attempt ${attempt} failed:`, error.message)
            
            if (attempt === maxRetries) {
                throw new Error(`Failed to send message after ${maxRetries} attempts: ${error.message}`)
            }

            // Exponential backoff
            const delay = Math.pow(2, attempt) * 1000
            console.log(`⏳ Waiting ${delay}ms before retry...`)
            await new Promise(resolve => setTimeout(resolve, delay))
        }
    }
}

// Usage
try {
    await sendMessageWithRetry(jid, { text: "Hello with retry logic!" })
    console.log("✅ Message sent successfully")
} catch (error) {
    console.error("❌ Final send failed:", error.message)
}
```

### Connection Management
```javascript
let reconnectAttempts = 0
const maxReconnectAttempts = 5

sock.ev.on("connection.update", ({ connection, lastDisconnect }) => {
    if (connection === "close") {
        const shouldReconnect = (lastDisconnect?.error as Boom)?.output?.statusCode !== DisconnectReason.loggedOut
        
        if (shouldReconnect && reconnectAttempts < maxReconnectAttempts) {
            reconnectAttempts++
            const delay = Math.min(1000 * Math.pow(2, reconnectAttempts), 30000)
            
            console.log(`🔄 Reconnecting... (attempt ${reconnectAttempts}/${maxReconnectAttempts})`)
            setTimeout(connectToWhatsApp, delay)
        } else {
            console.log("❌ Max reconnection attempts reached or logged out")
            process.exit(1)
        }
    } else if (connection === "open") {
        reconnectAttempts = 0 // reset on successful connection
        console.log("✅ Connected successfully!")
    }
})
```

### Message Queue System
```javascript
class MessageQueue {
    constructor(sock) {
        this.sock = sock
        this.queue = []
        this.isProcessing = false
        this.delay = 2000 // 2 seconds between messages
    }

    add(jid, content) {
        this.queue.push({ jid, content, timestamp: Date.now() })
        this.process()
    }

    async process() {
        if (this.isProcessing || this.queue.length === 0) return

        this.isProcessing = true

        while (this.queue.length > 0) {
            const { jid, content } = this.queue.shift()
            
            try {
                await this.sock.sendMessage(jid, content)
                console.log("✅ Message sent from queue")
            } catch (error) {
                console.error("❌ Queue send failed:", error)
                // Could re-add to queue or handle differently
            }

            // Wait before next message
            await new Promise(resolve => setTimeout(resolve, this.delay))
        }

        this.isProcessing = false
    }
}

// Usage
const messageQueue = new MessageQueue(sock)
messageQueue.add(jid, { text: "Queued message 1" })
messageQueue.add(jid, { text: "Queued message 2" })
```

### Rate Limiting
```javascript
class RateLimiter {
    constructor(limit = 10, window = 60000) { // 10 messages per minute
        this.limit = limit
        this.window = window
        this.requests = new Map()
    }

    canSend(jid) {
        const now = Date.now()
        const requests = this.requests.get(jid) || []
        
        // Remove old requests outside the window
        const validRequests = requests.filter(time => now - time < this.window)
        
        this.requests.set(jid, validRequests)
        return validRequests.length < this.limit
    }

    recordRequest(jid) {
        const requests = this.requests.get(jid) || []
        requests.push(Date.now())
        this.requests.set(jid, requests)
    }
}

const rateLimiter = new RateLimiter(15, 60000) // 15 messages per minute

// Usage in message handler
sock.ev.on("messages.upsert", async ({ messages }) => {
    for (const message of messages) {
        const jid = message.key.remoteJid
        
        if (!rateLimiter.canSend(jid)) {
            console.log("⚠️ Rate limit exceeded for", jid)
            continue
        }

        // Process message
        await sock.sendMessage(jid, { text: "Response message" })
        rateLimiter.recordRequest(jid)
    }
})
```

---

## 🎯 Advanced Features

### Story/Status Management
```javascript
// Upload status
await sock.sendMessage("status@broadcast", {
    image: { url: "./status-image.jpg" },
    caption: "Check out our latest update! 🚀"
})

// Text status
await sock.sendMessage("status@broadcast", {
    text: "Working on something amazing! 💻✨",
    backgroundColor: "#FF6B6B", // background color
    font: 3 // font style
})

// Video status
await sock.sendMessage("status@broadcast", {
    video: { url: "./status-video.mp4" },
    caption: "Behind the scenes 🎬"
})

// Get status updates
sock.ev.on("messages.upsert", ({ messages }) => {
    messages.forEach(msg => {
        if (msg.key.remoteJid === "status@broadcast") {
            console.log("📱 New status update:", msg)
        }
    })
})
```

### Broadcast Messages
```javascript
// Create broadcast list
const broadcastJids = [
    "6281234567890@s.whatsapp.net",
    "6289876543210@s.whatsapp.net",
    "6285555555555@s.whatsapp.net"
]

// Send broadcast message
async function sendBroadcast(content) {
    const results = []
    
    for (const jid of broadcastJids) {
        try {
            await new Promise(resolve => setTimeout(resolve, 2000)) // 2s delay
            const result = await sock.sendMessage(jid, content)
            results.push({ jid, success: true, result })
            console.log(`✅ Broadcast sent to ${jid}`)
        } catch (error) {
            results.push({ jid, success: false, error: error.message })
            console.log(`❌ Broadcast failed to ${jid}:`, error.message)
        }
    }
    
    return results
}

// Usage
const broadcastResults = await sendBroadcast({
    text: "🎉 Important announcement!\n\nOur new panel is now live at https://yupra.my.id",
    contextInfo: {
        externalAdReply: {
            title: "Yupra Panel Launch",
            body: "High-performance server management",
            thumbnailUrl: "https://yupra.my.id/assets/banner.png",
            sourceUrl: "https://yupra.my.id"
        }
    }
})

console.log("📊 Broadcast results:", broadcastResults)
```

### Advanced Media with Context
```javascript
// Image with rich context
await sock.sendMessage(jid, {
    image: { url: "https://yupra.my.id/assets/product.jpg" },
    caption: "🚀 New server deployment ready!",
    contextInfo: {
        externalAdReply: {
            title: "High-Performance Server",
            body: "Pterodactyl Panel • 99.9% Uptime",
            thumbnailUrl: "https://yupra.my.id/assets/logo.png",
            sourceUrl: "https://yupra.my.id",
            mediaType: 1,
            renderLargerThumbnail: true
        }
    }
})

// Video with preview
await sock.sendMessage(jid, {
    video: { url: "./demo-video.mp4" },
    caption: "🎥 Watch our panel demo",
    contextInfo: {
        externalAdReply: {
            title: "Panel Demo Video",
            body: "See how easy server management can be",
            thumbnailUrl: "https://yupra.my.id/video-thumb.jpg",
            sourceUrl: "https://yupra.my.id/demo",
            mediaType: 2
        }
    }
})

// Audio with metadata
await sock.sendMessage(jid, {
    audio: { url: "./notification-sound.mp3" },
    mimetype: "audio/mp3",
    contextInfo: {
        externalAdReply: {
            title: "System Alert",
            body: "Server notification sound",
            thumbnailUrl: "https://yupra.my.id/audio-icon.png",
            sourceUrl: "https://yupra.my.id",
            mediaType: 1
        }
    }
})
```

### Poll Messages
```javascript
// Create poll
await sock.sendMessage(jid, {
    poll: {
        name: "Which feature should we prioritize next?",
        values: [
            "🚀 Performance optimization",
            "🎨 UI/UX improvements", 
            "🔐 Security enhancements",
            "📱 Mobile app",
            "🤖 AI integration"
        ],
        selectableCount: 1 // single choice
    }
})

// Multiple choice poll
await sock.sendMessage(jid, {
    poll: {
        name: "What services are you interested in? (Multiple choice)",
        values: [
            "Web Development",
            "Mobile Apps",
            "Server Hosting",
            "Database Management",
            "DevOps Services"
        ],
        selectableCount: 3 // allow up to 3 choices
    }
})

// Handle poll responses
sock.ev.on("messages.upsert", ({ messages }) => {
    messages.forEach(msg => {
        if (msg.message?.pollUpdateMessage) {
            const pollUpdate = msg.message.pollUpdateMessage
            console.log("📊 Poll vote received:", pollUpdate)
        }
    })
})
```

### Newsletter Management
```javascript
// Create newsletter
const newsletter = await sock.newsletterCreate({
    name: "Yupra Tech Updates",
    description: "Latest news and updates from Yupra Network",
    picture: fs.readFileSync("./newsletter-cover.jpg")
})

// Follow newsletter  
await sock.newsletterFollow(newsletter.id)

// Send newsletter message
await sock.sendMessage(newsletter.id, {
    text: "🚀 Monthly Tech Update\n\nNew features launched this month:\n• Enhanced performance\n• Better security\n• Mobile optimization",
    contextInfo: {
        isNewsletterMessage: true
    }
})

// Unfollow newsletter
await sock.newsletterUnfollow(newsletter.id)
```

---

## 🔍 Message Parsing & Utilities

### Message Parser
```javascript
class MessageParser {
    static extractText(message) {
        return message?.conversation || 
               message?.extendedTextMessage?.text ||
               message?.imageMessage?.caption ||
               message?.videoMessage?.caption || ""
    }

    static getMessageType(message) {
        return Object.keys(message)[0]
    }

    static isMedia(message) {
        const mediaTypes = ['imageMessage', 'videoMessage', 'audioMessage', 'documentMessage', 'stickerMessage']
        return mediaTypes.includes(this.getMessageType(message))
    }

    static extractMentions(message) {
        const text = this.extractText(message)
        const mentions = text.match(/@(\d+)/g) || []
        return mentions.map(mention => mention.replace('@', '') + '@s.whatsapp.net')
    }

    static extractUrls(message) {
        const text = this.extractText(message)
        const urlRegex = /(https?:\/\/[^\s]+)/g
        return text.match(urlRegex) || []
    }

    static isCommand(message, prefix = '!') {
        const text = this.extractText(message)
        return text.startsWith(prefix)
    }

    static parseCommand(message, prefix = '!') {
        const text = this.extractText(message)
        if (!this.isCommand(message, prefix)) return null

        const args = text.slice(prefix.length).trim().split(' ')
        const command = args.shift().toLowerCase()
        
        return { command, args }
    }
}

// Usage
sock.ev.on("messages.upsert", ({ messages }) => {
    messages.forEach(msg => {
        if (!msg.message) return

        const text = MessageParser.extractText(msg.message)
        const type = MessageParser.getMessageType(msg.message)
        
        console.log(`📝 ${type}: ${text}`)

        if (MessageParser.isCommand(msg.message)) {
            const { command, args } = MessageParser.parseCommand(msg.message)
            handleCommand(msg.key.remoteJid, command, args, msg)
        }
    })
})

async function handleCommand(jid, command, args, message) {
    switch (command) {
        case 'ping':
            await sock.sendMessage(jid, { text: '🏓 Pong!' }, { quoted: message })
            break
            
        case 'info':
            await sock.sendMessage(jid, {
                text: '🤖 Bot Information:\n' +
                      '• Version: 2.0.0\n' +
                      '• Powered by: @yupra/baileys\n' +
                      '• Uptime: ' + process.uptime() + 's'
            })
            break
            
        case 'weather':
            const city = args.join(' ') || 'Yupra'
            // Implement weather API call
            await sock.sendMessage(jid, { 
                text: `🌤️ Weather for ${city}: 28°C, Sunny` 
            })
            break
    }
}
```

### File Download Utility
```javascript
import { downloadMediaMessage } from "@yupra/baileys"

async function downloadAndSaveMedia(message) {
    try {
        const buffer = await downloadMediaMessage(
            message,
            'buffer',
            {},
            { 
                logger: P({ level: 'silent' }),
                reuploadRequest: sock.updateMediaMessage
            }
        )

        const messageType = Object.keys(message.message)[0]
        let extension = 'bin'
        
        switch (messageType) {
            case 'imageMessage': extension = 'jpg'; break
            case 'videoMessage': extension = 'mp4'; break
            case 'audioMessage': extension = 'mp3'; break
            case 'documentMessage': 
                extension = message.message.documentMessage.fileName?.split('.').pop() || 'pdf'
                break
        }

        const fileName = `downloads/${Date.now()}.${extension}`
        fs.writeFileSync(fileName, buffer)
        
        console.log(`💾 Media saved as: ${fileName}`)
        return { success: true, fileName, buffer }
    } catch (error) {
        console.error('❌ Download failed:', error)
        return { success: false, error: error.message }
    }
}

// Usage
sock.ev.on("messages.upsert", async ({ messages }) => {
    for (const msg of messages) {
        if (!msg.message) continue

        if (MessageParser.isMedia(msg.message)) {
            const result = await downloadAndSaveMedia(msg)
            if (result.success) {
                await sock.sendMessage(msg.key.remoteJid!, {
                    text: `✅ Media downloaded: ${result.fileName}`
                })
            }
        }
    }
})
```

---

## 🔧 Configuration & Environment

### Environment Configuration
```javascript
// .env file
/*
PHONE_NUMBER=6281234567890
SESSION_PATH=./auth_session
WEBHOOK_URL=https://your-webhook.com/whatsapp
DATABASE_URL=mongodb://localhost:27017/whatsapp_bot
REDIS_URL=redis://localhost:6379
LOG_LEVEL=info
ADMIN_NUMBERS=6281234567890,6289876543210
*/

import dotenv from 'dotenv'
dotenv.config()

const config = {
    phoneNumber: process.env.PHONE_NUMBER,
    sessionPath: process.env.SESSION_PATH || './auth_info',
    webhookUrl: process.env.WEBHOOK_URL,
    databaseUrl: process.env.DATABASE_URL,
    redisUrl: process.env.REDIS_URL,
    logLevel: process.env.LOG_LEVEL || 'info',
    adminNumbers: process.env.ADMIN_NUMBERS?.split(',') || []
}

// Advanced socket configuration
const sock = makeWASocket({
    auth: state,
    printQRInTerminal: true,
    logger: P({ level: config.logLevel }),
    browser: ["YupraBaileys", "Chrome", "4.0.0"],
    generateHighQualityLinkPreview: true,
    syncFullHistory: false,
    markOnlineOnConnect: true,
    fireInitQueries: true,
    emitOwnEvents: false,
    defaultQueryTimeoutMs: 60000,
    keepAliveIntervalMs: 30000,
    connectTimeoutMs: 30000,
    qrTimeout: 45000,
    msgRetryCounterCache: {},
    shouldSyncHistoryMessage: () => true,
    shouldIgnoreJid: (jid) => isJidBroadcast(jid),
    linkPreviewImageThumbnailWidth: 192,
    transactionOpts: { 
        maxCommitRetries: 10, 
        delayBetweenTriesMs: 3000 
    },
    getMessage: async (key) => {
        if (store) {
            const msg = await store.loadMessage(key.remoteJid!, key.id!)
            return msg?.message || undefined
        }
        return proto.Message.fromObject({})
    }
})
```

### Database Integration
```javascript
// MongoDB integration example
import { MongoClient } from 'mongodb'

class DatabaseManager {
    constructor(url) {
        this.client = new MongoClient(url)
        this.db = null
    }

    async connect() {
        await this.client.connect()
        this.db = this.client.db('whatsapp_bot')
        console.log('📦 Connected to MongoDB')
    }

    async saveMessage(message) {
        const collection = this.db.collection('messages')
        await collection.insertOne({
            ...message,
            timestamp: new Date(),
            processed: false
        })
    }

    async saveUser(jid, userData) {
        const collection = this.db.collection('users')
        await collection.updateOne(
            { jid },
            { $set: { ...userData, lastSeen: new Date() } },
            { upsert: true }
        )
    }

    async getUser(jid) {
        const collection = this.db.collection('users')
        return await collection.findOne({ jid })
    }

    async saveChat(chatData) {
        const collection = this.db.collection('chats')
        await collection.updateOne(
            { id: chatData.id },
            { $set: chatData },
            { upsert: true }
        )
    }
}

const dbManager = new DatabaseManager(config.databaseUrl)
await dbManager.connect()

// Integration with message handler
sock.ev.on("messages.upsert", async ({ messages }) => {
    for (const message of messages) {
        await dbManager.saveMessage(message)
        
        if (message.key.fromMe) continue
        
        const jid = message.key.remoteJid
        await dbManager.saveUser(jid, {
            jid,
            pushName: message.pushName,
            lastMessage: MessageParser.extractText(message.message)
        })
    }
})
```

---

## 🚀 Production Deployment

### Docker Configuration
```dockerfile
# Dockerfile
FROM node:18-alpine

WORKDIR /app

# Install dependencies
COPY package*.json ./
RUN npm ci --only=production

# Copy application
COPY . .

# Create directories
RUN mkdir -p auth_session downloads logs

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node healthcheck.js

# Start application
CMD ["node", "index.js"]
```

```yaml
# docker-compose.yml
version: '3.8'

services:
  whatsapp-bot:
    build: .
    restart: unless-stopped
    volumes:
      - ./auth_session:/app/auth_session
      - ./downloads:/app/downloads
      - ./logs:/app/logs
    environment:
      - NODE_ENV=production
      - PHONE_NUMBER=${PHONE_NUMBER}
      - DATABASE_URL=${DATABASE_URL}
      - REDIS_URL=redis://redis:6379
    depends_on:
      - redis
      - mongodb
    networks:
      - bot-network

  redis:
    image: redis:7-alpine
    restart: unless-stopped
    volumes:
      - redis_data:/data
    networks:
      - bot-network

  mongodb:
    image: mongo:6
    restart: unless-stopped
    volumes:
      - mongo_data:/data/db
    environment:
      - MONGO_INITDB_ROOT_USERNAME=${MONGO_USER}
      - MONGO_INITDB_ROOT_PASSWORD=${MONGO_PASSWORD}
    networks:
      - bot-network

volumes:
  redis_data:
  mongo_data:

networks:
  bot-network:
    driver: bridge
```

### Process Manager (PM2)
```javascript
// ecosystem.config.js
module.exports = {
    apps: [{
        name: 'whatsapp-bot',
        script: './index.js',
        instances: 1,
        exec_mode: 'fork',
        watch: false,
        max_memory_restart: '1G',
        env: {
            NODE_ENV: 'production',
            PORT: 3000
        },
        error_file: './logs/err.log',
        out_file: './logs/out.log',
        log_file: './logs/combined.log',
        time: true,
        autorestart: true,
        max_restarts: 10,
        min_uptime: '10s',
        restart_delay: 4000
    }]
}
```

### Logging System
```javascript
import winston from 'winston'

const logger = winston.createLogger({
    level: config.logLevel,
    format: winston.format.combine(
        winston.format.timestamp(),
        winston.format.errors({ stack: true }),
        winston.format.json()
    ),
    defaultMeta: { service: 'whatsapp-bot' },
    transports: [
        new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
        new winston.transports.File({ filename: 'logs/combined.log' }),
        new winston.transports.Console({
            format: winston.format.combine(
                winston.format.colorize(),
                winston.format.simple()
            )
        })
    ]
})

// Usage throughout the application
logger.info('🚀 Bot starting...')
logger.error('❌ Connection failed', { error: error.message })
logger.warn('⚠️ Rate limit exceeded for user', { jid })
```

---

## 📜 License & Credits

**License:** MIT / GPL-3.0  
**Maintainer:** [Yupra Network](https://yupra.my.id)  
**Inspired by:** Baileys Community  
**Repository:** [@yupra/baileys on NPM](https://www.npmjs.com/package/@yupra/baileys)

### 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### 📞 Support

- 🌐 **Website:** [https://yupra.my.id/](https://yupra.my.id/)
- 💬 **Telegram:** [@yupra_support](https://t.me/yupra_support)
- 📧 **Email:** support@yupra.my.id
- 🐛 **Issues:** [GitHub Issues](https://github.com/yupra-network/baileys/issues)

### ⭐ Show Your Support

Give a ⭐️ if this project helped you!

### 🙏 Acknowledgments

- WhatsApp for the amazing platform
- The original Baileys contributors
- The Node.js and TypeScript communities
- All developers using and contributing to @yupra/baileys

---

## 📊 Performance Tips

### Memory Optimization
```javascript
// Optimize memory usage
const sock = makeWASocket({
    // ... other options
    syncFullHistory: false, // Don't sync full chat history
    emitOwnEvents: false,   // Don't emit events for own messages
    shouldSyncHistoryMessage: (msg) => {
        // Only sync recent messages
        const twoDaysAgo = Date.now() - (2 * 24 * 60 * 60 * 1000)
        return msg.messageTimestamp * 1000 > twoDaysAgo
    },
    msgRetryCounterCache: new NodeCache({ 
        stdTTL: 300, // 5 minutes
        checkperiod: 60 
    })
})

// Clean up old messages periodically
setInterval(() => {
    if (store) {
        const cutoff = Date.now() - (7 * 24 * 60 * 60 * 1000) // 7 days
        store.cleanupMessages(cutoff)
    }
}, 24 * 60 * 60 * 1000) // daily cleanup
```

### Connection Optimization
```javascript
// Optimize connection settings
const connectionOpts = {
    connectTimeoutMs: 60000,
    defaultQueryTimeoutMs: 60000,
    keepAliveIntervalMs: 30000,
    qrTimeout: 45000,
    maxMsgRetryCount: 5,
    retryRequestDelayMs: 250,
    fireInitQueries: true,
    markOnlineOnConnect: false, // Don't auto-mark online
    syncFullHistory: false
}

// Connection health monitoring
let lastPong = Date.now()
setInterval(async () => {
    try {
        await sock.query({
            tag: 'iq',
            attrs: { type: 'get', xmlns: 'w:p', to: 'g.us' }
        })
        lastPong = Date.now()
    } catch (error) {
        const timeSinceLastPong = Date.now() - lastPong
        if (timeSinceLastPong > 60000) { // 1 minute
            console.log('🔄 Connection seems unhealthy, reconnecting...')
            sock.end(new Error('Health check failed'))
        }
    }
}, 30000) // Check every 30 seconds
```

---

**Made with ❤️ by https://yupra.my.id**
