<script lang="ts">
  import OtpInput from "$lib/components/OtpInput.svelte";

  // Demo states
  let otp1: string[] = [];
  let otp2: string[] = [];
  let otp3: string[] = [];
  let otp4: string[] = [];
  let otp5: string[] = [];
  
  let otp1Ref: OtpInput;
  let otp2Ref: OtpInput;
  let otp3Ref: OtpInput;
  let otp4Ref: OtpInput;
  let otp5Ref: OtpInput;

  // Demo functions
  function resetAll() {
    otp1Ref?.reset();
    otp2Ref?.reset();
    otp3Ref?.reset();
    otp4Ref?.reset();
    otp5Ref?.reset();
  }

  function setDemoValue() {
    otp1Ref?.setValue("123456");
  }

  function focusFirst() {
    otp1Ref?.focus();
  }
</script>

<svelte:head>
  <title>OTP Input Component Demo</title>
</svelte:head>

<div class="demo-container">
  <h1>OTP Input Component Demo</h1>
  <p>This page demonstrates the reusable OTP input component with different configurations.</p>

  <div class="demo-section">
    <h2>1. Standard 6-digit OTP</h2>
    <p>Default configuration with 6 digits</p>
    <OtpInput
      bind:this={otp1Ref}
      bind:value={otp1}
      on:complete={(e) => console.log('OTP 1 complete:', e.detail.value)}
    />
    <p>Value: {otp1.join('') || 'Empty'}</p>
  </div>

  <div class="demo-section">
    <h2>2. 4-digit OTP</h2>
    <p>Shorter OTP with 4 digits</p>
    <OtpInput
      bind:this={otp2Ref}
      length={4}
      bind:value={otp2}
      on:complete={(e) => console.log('OTP 2 complete:', e.detail.value)}
    />
    <p>Value: {otp2.join('') || 'Empty'}</p>
  </div>

  <div class="demo-section">
    <h2>3. 8-digit OTP with Error State</h2>
    <p>Longer OTP with error styling</p>
    <OtpInput
      bind:this={otp3Ref}
      length={8}
      bind:value={otp3}
      error={true}
      on:complete={(e) => console.log('OTP 3 complete:', e.detail.value)}
    />
    <p>Value: {otp3.join('') || 'Empty'}</p>
  </div>

  <div class="demo-section">
    <h2>4. Disabled OTP Input</h2>
    <p>OTP input in disabled state</p>
    <OtpInput
      bind:this={otp4Ref}
      bind:value={otp4}
      disabled={true}
    />
    <p>Value: {otp4.join('') || 'Empty'}</p>
  </div>

  <div class="demo-section">
    <h2>5. Custom Placeholder</h2>
    <p>OTP with custom placeholder character</p>
    <OtpInput
      bind:this={otp5Ref}
      bind:value={otp5}
      placeholder="•"
      on:complete={(e) => console.log('OTP 5 complete:', e.detail.value)}
    />
    <p>Value: {otp5.join('') || 'Empty'}</p>
  </div>

  <div class="demo-controls">
    <h2>Demo Controls</h2>
    <div class="button-group">
      <button on:click={resetAll}>Reset All</button>
      <button on:click={setDemoValue}>Set Demo Value (123456)</button>
      <button on:click={focusFirst}>Focus First Input</button>
    </div>
  </div>

  <div class="demo-info">
    <h2>Component Features</h2>
    <ul>
      <li>✅ Configurable length (4, 6, 8 digits, etc.)</li>
      <li>✅ Auto-focus next input on digit entry</li>
      <li>✅ Backspace navigation</li>
      <li>✅ Arrow key navigation</li>
      <li>✅ Paste support (distributes digits across inputs)</li>
      <li>✅ Error state styling</li>
      <li>✅ Disabled state</li>
      <li>✅ Custom placeholder</li>
      <li>✅ Responsive design</li>
      <li>✅ Accessibility features (ARIA labels)</li>
      <li>✅ Event dispatching (change, complete, focus, paste)</li>
      <li>✅ Public methods (reset, focus, getValue, setValue)</li>
    </ul>
  </div>
</div>

<style>
  .demo-container {
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  }

  h1 {
    color: #333;
    text-align: center;
    margin-bottom: 10px;
  }

  .demo-section {
    margin: 30px 0;
    padding: 20px;
    border: 1px solid #e1e5e9;
    border-radius: 8px;
    background: #fafafa;
  }

  .demo-section h2 {
    color: #555;
    margin-top: 0;
    margin-bottom: 10px;
  }

  .demo-section p {
    color: #666;
    margin: 5px 0;
  }

  .demo-controls {
    margin: 30px 0;
    padding: 20px;
    border: 1px solid #007bff;
    border-radius: 8px;
    background: #f0f8ff;
  }

  .button-group {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
  }

  .button-group button {
    padding: 10px 20px;
    border: none;
    border-radius: 6px;
    background: #007bff;
    color: white;
    cursor: pointer;
    font-size: 14px;
    transition: background 0.2s;
  }

  .button-group button:hover {
    background: #0056b3;
  }

  .demo-info {
    margin: 30px 0;
    padding: 20px;
    border: 1px solid #28a745;
    border-radius: 8px;
    background: #f8fff9;
  }

  .demo-info h2 {
    color: #28a745;
    margin-top: 0;
  }

  .demo-info ul {
    list-style: none;
    padding: 0;
  }

  .demo-info li {
    padding: 5px 0;
    color: #555;
  }

  @media (max-width: 600px) {
    .demo-container {
      padding: 10px;
    }
    
    .button-group {
      flex-direction: column;
    }
  }
</style>
