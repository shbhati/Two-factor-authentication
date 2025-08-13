<script lang="ts">
  import unlock from "$lib/assets/unlock.png";
  import lock from "$lib/assets/lock.png";
  import wrong from "$lib/assets/locked.png";
  // State management
  const LENGTH = 6;

  // Static OTP for validation
  const STATIC_OTP = "786786";

  // Main state
  let otpValues = Array(LENGTH).fill("");
  let isComplete = false;
  let remainingDigits = LENGTH;
  let currentFocusIndex = 0;
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

  // State update functions
  function updateDigit(index: number, value: string) {
    otpValues[index] = value;
    otpValues = [...otpValues]; // Trigger reactivity
  }

  function clearDigit(index: number) {
    updateDigit(index, "");
  }

  function setFocusIndex(index: number) {
    currentFocusIndex = Math.max(0, Math.min(index, LENGTH - 1));
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

  // Input handlers
  function handleInput(i: number, event: Event) {
    const el = event.target as HTMLInputElement;
    const digit = el.value.replace(/\D/g, "").slice(0, 1); // only 0–9, max 1 char

    updateDigit(i, digit);

    // Auto-move to next on valid digit
    if (digit && i < LENGTH - 1) {
      setFocusIndex(i + 1);
      focusInput(i + 1);
    }
  }

  function handleKeydown(i: number, event: KeyboardEvent) {
    const el = event.target as HTMLInputElement;

    // If typing a digit into a filled box, clear first so new one replaces it
    if (/^\d$/.test(event.key) && el.value) {
      clearDigit(i);
    }

    if (event.key === "Backspace") {
      event.preventDefault();
      if (el.value) {
        clearDigit(i);
        if (i > 0) {
          setFocusIndex(i - 1);
          focusInput(i - 1);
        }
      } else if (i > 0) {
        setFocusIndex(i - 1);
        focusInput(i - 1);
        clearDigit(i - 1);
        const prevEl = document.getElementById(
          `otp-${i - 1}`,
        ) as HTMLInputElement;
        if (prevEl) {
          prevEl.value = "";
        }
      }
    }

    if (event.key === "ArrowLeft" && i > 0) {
      setFocusIndex(i - 1);
      focusInput(i - 1);
    }
    if (event.key === "ArrowRight" && i < LENGTH - 1) {
      setFocusIndex(i + 1);
      focusInput(i + 1);
    }

    const allowed =
      ["Backspace", "Tab", "ArrowLeft", "ArrowRight", "Delete"].includes(
        event.key,
      ) ||
      event.ctrlKey ||
      event.metaKey;

    if (!allowed && !/^\d$/.test(event.key)) {
      event.preventDefault();
    }
  }

  // Paste: distribute only digits across remaining boxes
  function handlePaste(i: number, event: ClipboardEvent) {
    event.preventDefault();
    const text = event.clipboardData?.getData("text") || "";
    const digits = text
      .replace(/\D/g, "")
      .split("")
      .slice(0, LENGTH - i);

    // Update state
    digits.forEach((d: string, idx: number) => {
      updateDigit(i + idx, d);
    });

    // Update DOM values + set focus
    queueMicrotask(() => {
      for (let k = i; k < i + digits.length; k++) {
        const el = document.getElementById(`otp-${k}`) as HTMLInputElement;
        if (el) el.value = otpValues[k];
      }
      const nextIndex = Math.min(i + digits.length, LENGTH - 1);
      setFocusIndex(nextIndex);
      focusInput(nextIndex);
    });
  }

  function focusInput(i: number) {
    const el = document.getElementById(`otp-${i}`);
    if (el) el.focus();
  }

  // Reset state function
  function resetOTP() {
    otpValues = Array(LENGTH).fill("");
    setFocusIndex(0);
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

  <div class="code-inputs">
    {#each Array(LENGTH) as _, i}
      <input
        class={isComplete && !isOtpCorrect ? "error" : ""}
        id={"otp-" + i}
        type="text"
        maxlength="1"
        inputmode="numeric"
        pattern="\d*"
        aria-label={"Digit " + (i + 1)}
        value={otpValues[i]}
        on:focus={(e) => {
          (e.target as HTMLInputElement)?.select();
          setFocusIndex(i);
        }}
        on:input={(e) => handleInput(i, e)}
        on:keydown={(e) => handleKeydown(i, e)}
        on:paste={(e) => handlePaste(i, e)}
      />
    {/each}
  </div>

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
