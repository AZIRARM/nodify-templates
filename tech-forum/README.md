# Nodify Tech Forum Template

A complete **developer community forum** template for [Nodify Headless CMS](https://github.com/AZIRARM/nodify). This template creates a fully functional discussion forum where developers can share knowledge, ask questions, and engage in technical discussions.

## 🚀 Features

- **📱 Fully Responsive** - Works on desktop, tablet, and mobile devices
- **💬 Interactive Forum** - Create topics, post replies, and engage in discussions
- **🏷️ Language Badges** - Automatic badge detection for Java, Angular, Python, and PHP
- **🎨 Modern UI** - Gradient design with glassmorphism effects
- **📊 Real-time Updates** - Reply counts update dynamically
- **🔒 Built-in Rules** - Maintenance mode and activation/deactivation controls

## 📋 Template Structure

The template includes:

### Site Node: `DEV-A76412A5`
- **Style Node** (`DEV-A0D075D8`) - CSS styling for the forum
- **Script Node** (`DEV-1D42BB40`) - JavaScript functionality
- **HTML Node** (`DEV-571A4D3C`) - Main forum interface

### Folder Node: `TOPICS-E8B558E3`
Contains 4 pre-configured discussion topics:
- ☕ Java - Best practices for Spring Boot
- 🅰 Angular - Signals vs RxJS
- 🐍 Python - FastAPI vs Django
- 🐘 PHP - Laravel 11 new features

## 🔧 How to Use

### 1. Import the Template
- Open your **Nodify dashboard**
- Go to **Projects → Environments**
- Select your environment
- Click the **import button** (⬇️ inside a circle)
- Upload the `Nodify-Tech-Forum.json` file

### 2. Configure the Forum
The template is ready to use immediately. The forum structure includes:
- A main site with forum interface
- A folder containing discussion topics
- Each topic stores replies as data entries

### 3. Access Your Forum
Navigate to the published HTML node (`DEV-571A4D3C`) to view your forum.

## 🎯 How It Works

### Topics
Each topic is a content node with:
- **Title** - Discussion subject
- **Description** - Brief overview
- **Author** - Topic creator (stored in values)
- **Replies** - Stored as data entries linked to the topic

### Replies
When users post replies:
1. Reply data is saved to the Nodify data API
2. The reply appears immediately in the discussion thread
3. Reply count updates automatically in the topic list

### Data Structure
Each reply contains:
- `contentNodeCode` - Link to the topic
- `value` - Reply content
- `user` - Author name
- `creationDate` - Timestamp

## 🎨 Customization

### Modify Styles
Edit the style node (`DEV-A0D075D8`) to customize:
- Colors and gradients
- Fonts and typography
- Layout and spacing
- Animation effects

### Add New Topics
1. Create a new content node under the `TOPICS-E8B558E3` folder
2. Set type to `HTML`
3. Add title and description
4. Add an `AUTHOR` value with the creator's name
5. Publish the topic

### Add Language Badges
The forum automatically displays badges for topics containing:
- `java` → ☕ Java badge
- `angular` → 🅰 Angular badge
- `python` → 🐍 Python badge
- `php` → 🐘 PHP badge

To add more language badges, update the `getLanguageBadge()` function in the script node.

## 🛠️ Technical Details

### Dependencies
- **Google Fonts** - Fira Code font for code-friendly typography
- **Nodify API** - Uses built-in content and data endpoints

### API Endpoints Used
- `/contents/code/{code}` - Fetch topic details
- `/contents/node/code/{code}` - List all topics
- `/datas/` - Save new replies
- `/datas/contentCode/{code}` - Fetch replies for a topic
- `/datas/content-code/{code}/count` - Get reply count

### Rules System
Each node includes built-in rules:
- `MAINTENANCE` - Toggle maintenance mode
- `ACTIVATION_DATE` - Schedule activation
- `DEACTIVATION_DATE` - Schedule deactivation

## 📱 Responsive Design

The forum is fully responsive and adapts to:
- Desktop (1400px+)
- Tablet (768px - 1400px)
- Mobile (<768px)

## 🔐 Permissions

The template works with default Nodify permissions. For production use, consider:
- Setting up authentication for posting replies
- Adding moderation capabilities
- Implementing spam protection

## 🐛 Troubleshooting

**Topics not loading?**
- Ensure the `TOPICS-E8B558E3` node code is correct in the JavaScript
- Check that topics are published (status = SNAPSHOT)

**Replies not saving?**
- Verify data API permissions
- Check browser console for errors

**Badges not showing?**
- Ensure topic titles contain the language keywords
- Check JavaScript console for errors

## 📝 Notes

- The template is designed for community engagement and discussion
- All data is stored in your Nodify instance - no external databases needed
- Reply counts update dynamically when new messages are posted
- The forum supports unlimited topics and replies

## 🎉 Ready to Use

Your developer community forum is now ready! Start by:
1. Adding more programming language topics
2. Encouraging community participation
3. Customizing the design to match your brand
4. Setting up user authentication for secure posting
