# OTP Input Component Demo

A modern, interactive OTP (One-Time Password) input component built with Svelte 5. This project demonstrates a clean, user-friendly interface for entering 6-digit authentication codes with real-time validation and visual feedback.

## 🚀 Features

- **Interactive OTP Input**: 6-digit code input with individual input boxes
- **Real-time Validation**: Instant feedback on correct/incorrect OTP
- **Visual Feedback**: Dynamic icons (lock/unlock/wrong) based on input status
- **Keyboard Navigation**: Arrow keys, backspace, and paste support
- **Auto-focus Management**: Automatic focus movement between input fields
- **Paste Support**: Paste a 6-digit code to auto-fill all fields
- **Responsive Design**: Clean, modern UI with smooth animations
- **TypeScript Support**: Fully typed for better development experience

## 🎯 Demo

The demo application includes:
- A static OTP code (`786786`) for testing
- Visual feedback with lock/unlock icons
- Progress tracking showing remaining digits
- Success/error states with appropriate messaging

## 🛠️ Installation

1. **Clone the repository**:
   ```bash
   git clone <repository-url>
   cd assignment
   ```

2. **Install dependencies**:
   ```bash
   npm install
   ```

3. **Start the development server**:
   ```bash
   npm run dev
   ```

4. **Open your browser** and navigate to `http://localhost:5173`

## 📖 Usage

### Basic Usage

The OTP input component can be used in any Svelte application:

```svelte
<script>
  import OtpInput from '$lib/components/OtpInput.svelte';
  
  let otpValues = Array(6).fill("");
  let isComplete = false;
  let isOtpCorrect = false;
  
  function handleOtpUpdate(event) {
    otpValues = event.detail.values;
  }
  
  function handleOtpComplete(event) {
    // Handle OTP completion
    const otpString = event.detail.otpString;
    // Validate your OTP here
  }
</script>

<OtpInput
  length={6}
  value={otpValues}
  {isComplete}
  {isOtpCorrect}
  on:update={handleOtpUpdate}
  on:complete={handleOtpComplete}
/>
```

### Component Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `length` | `number` | `6` | Number of OTP digits |
| `value` | `string[]` | `[]` | Array of current OTP values |
| `isComplete` | `boolean` | `false` | Whether all digits are filled |
| `isOtpCorrect` | `boolean` | `false` | Whether the OTP is valid |

### Events

| Event | Detail | Description |
|-------|--------|-------------|
| `update` | `{values, index, digitValue}` | Fired when any digit is updated |
| `complete` | `{otpString, isComplete}` | Fired when all digits are filled |
| `reset` | - | Fired when OTP is reset |

### Keyboard Shortcuts

- **Arrow Keys**: Navigate between input fields
- **Backspace**: Clear current field and move to previous
- **Paste**: Paste a multi-digit code to auto-fill
- **Tab**: Navigate between fields
- **Delete**: Clear current field

## 🏗️ Project Structure

```
src/
├── lib/
│   ├── components/
│   │   └── OtpInput.svelte    # Main OTP input component
│   └── assets/
│       ├── lock.png          # Locked state icon
│       ├── unlock.png        # Unlocked state icon
│       └── locked.png        # Wrong OTP state icon
├── routes/
│   └── +page.svelte          # Demo application
└── app.html                  # HTML template
```

## 🎨 Customization

### Styling

The component uses CSS classes that can be customized:

- `.code-inputs`: Container for all input fields
- `.code-inputs input`: Individual input field styling
- `.code-inputs input.error`: Error state styling

### Icons

Replace the icon files in `src/lib/assets/` to customize the visual feedback:
- `lock.png`: Default state
- `unlock.png`: Success state
- `locked.png`: Error state

## 🚀 Building for Production

```bash
# Build the application
npm run build

# Preview the production build
npm run preview
```

## 🧪 Testing the Demo

1. Start the development server
2. Enter the demo OTP: `786786`
3. Watch the visual feedback change from lock → unlock
4. Try entering wrong codes to see error states
5. Test keyboard navigation and paste functionality

## 📱 Browser Support

- Modern browsers with ES6+ support
- Mobile browsers with touch input support
- Keyboard navigation support for accessibility

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

## 🆘 Support

If you encounter any issues or have questions:
1. Check the browser console for errors
2. Ensure all dependencies are installed
3. Verify you're using a compatible Node.js version
4. Open an issue on the repository

---

**Happy coding! 🔐✨**
