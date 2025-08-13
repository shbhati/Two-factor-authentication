<script lang="ts">
    import { createEventDispatcher } from "svelte";

    const dispatch = createEventDispatcher();

    // Props
    export let length: number = 6;
    export let value: string[] = [];
    export let isComplete: boolean = false;
    export let isOtpCorrect: boolean = false;
    export let remainingDigits: number = length;

    // State management
    let currentFocusIndex = 0;

    // Reactive statements for derived state
    $: remainingDigits = length - value.filter(Boolean).length;
    $: isComplete = remainingDigits === 0;
    $: otpString = value.join("");

    // Emit events when state changes
    $: if (isComplete) {
        dispatch("complete", { otpString, isComplete });
    }

    // State update functions
    function updateDigit(index: number, digitValue: string) {
        const newValues = [...value];
        newValues[index] = digitValue;
        dispatch("update", { values: newValues, index, digitValue });
    }

    function clearDigit(index: number) {
        updateDigit(index, "");
    }

    function setFocusIndex(index: number) {
        currentFocusIndex = Math.max(0, Math.min(index, length - 1));
    }

    // Input handlers
    function handleInput(i: number, event: Event) {
        const el = event.target as HTMLInputElement;
        const digit = el.value.replace(/\D/g, "").slice(0, 1); // only 0–9, max 1 char

        updateDigit(i, digit);

        // Auto-move to next on valid digit
        if (digit && i < length - 1) {
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
        if (event.key === "ArrowRight" && i < length - 1) {
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
            .slice(0, length - i);

        // Update state
        digits.forEach((d: string, idx: number) => {
            updateDigit(i + idx, d);
        });

        // Update DOM values + set focus
        queueMicrotask(() => {
            for (let k = i; k < i + digits.length; k++) {
                const el = document.getElementById(
                    `otp-${k}`,
                ) as HTMLInputElement;
                if (el) el.value = value[k];
            }
            const nextIndex = Math.min(i + digits.length, length - 1);
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
        dispatch("reset");
    }

    // Expose reset function
    export { resetOTP };
</script>

<div class="code-inputs">
    {#each Array(length) as _, i}
        <input
            class={isComplete && !isOtpCorrect ? "error" : ""}
            id={"otp-" + i}
            type="text"
            maxlength="1"
            inputmode="numeric"
            pattern="\d*"
            aria-label={"Digit " + (i + 1)}
            value={value[i]}
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
