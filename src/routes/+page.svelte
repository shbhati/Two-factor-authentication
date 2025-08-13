<script lang="ts">
  import unlock from "$lib/assets/unlock.png";
  import lock from "$lib/assets/lock.png";
  import wrong from "$lib/assets/locked.png";
  import OtpInput from "$lib/components/OtpInput.svelte";

  // State management
  const LENGTH = 6;

  // Static OTP for validation
  const STATIC_OTP = "786786";

  // Main state
  let otpValues = Array(LENGTH).fill("");
  let isComplete = false;
  let remainingDigits = LENGTH;
  let otpMessage = "";
  let isOtpCorrect = false;

  // Reactive statements for derived state
  $: remainingDigits = LENGTH - otpValues.filter(Boolean).length;
  $: isComplete = remainingDigits === 0;
  $: otpString = otpValues.join("");

  // Check OTP when complete
  $: if (isComplete) {
    checkOTP();
  }

  // OTP validation function
  function checkOTP() {
    if (otpString === STATIC_OTP) {
      otpMessage = "Correct OTP!";
      isOtpCorrect = true;
    } else {
      otpMessage = "Wrong OTP! Please try again.";
      isOtpCorrect = false;
    }
  }

  // Event handlers for OTP component
  function handleOtpUpdate(event: CustomEvent) {
    otpValues = event.detail.values;
  }

  function handleOtpComplete(event: CustomEvent) {
    // OTP is complete, validation will be triggered by reactive statement
  }

  function handleOtpReset() {
    otpValues = Array(LENGTH).fill("");
    isOtpCorrect = false;
  }
</script>

<div class="authenticatorCard">
  <div
    class={`icon ${isComplete && isOtpCorrect ? "icon success" : isComplete && !isOtpCorrect ? "icon wrong" : ""}`}
  >
    <img
      src={isComplete && isOtpCorrect
        ? unlock
        : isComplete && !isOtpCorrect
          ? wrong
          : lock}
      alt="lock"
    />
  </div>
  <h1>Easy peasy</h1>
  <p>Enter 6-digit code from your two factor authenticator APP.</p>

  <b> OTP: {STATIC_OTP}</b>

  <OtpInput
    length={LENGTH}
    value={otpValues}
    {isComplete}
    {isOtpCorrect}
    on:update={handleOtpUpdate}
    on:complete={handleOtpComplete}
    on:reset={handleOtpReset}
  />

  <button class={isComplete ? "lets-go" : ""} disabled={!isComplete}>
    {#if isOtpCorrect}
      <span>Let's go!</span>
    {:else}
      <span class="wrong-otp">Wrong Code</span>
    {/if}

    {remainingDigits > 0 ? remainingDigits : ""}
    {remainingDigits === 1 ? "digit" : "digits"} left
  </button>
</div>
