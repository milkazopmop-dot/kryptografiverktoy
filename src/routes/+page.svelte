<script lang="ts">
    let tekst = $state("");
    let nokkel = $state<number | undefined>(3);
    let resultat = $state("");
    let feil = $state("");

    const alfabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";

    function caesar(retning: number) {
        feil = "";
        resultat = "";

        if (!tekst.trim()) {
            feil = "Skriv inn en tekst.";
            return;
        }

        // Проверяем исходный текст: только A–Z, a–z и пробелы
        const tillatteTegn = alfabet + alfabet.toLowerCase() + " ";

        for (const tegn of tekst) {
            if (!tillatteTegn.includes(tegn)) {
                feil = "Bruk bare engelske bokstaver og mellomrom. Ikke bruk Enter.";
                return;
            }
        }

        if (nokkel === undefined) {
            feil = "Skriv inn en nøkkel.";
            return;
        }

        if (!Number.isInteger(nokkel) || nokkel < 0 || nokkel > 25) {
            feil = "Nøkkelen må være et heltall fra 0 til 25.";
            return;
        }

        let nyTekst = "";

        for (const tegn of tekst) {
            // Пробелы не шифруем
            if (tegn === " ") {
                nyTekst += " ";
                continue;
            }

            const indeks = alfabet.indexOf(tegn.toUpperCase());

            // 1 — сдвиг вперёд, -1 — сдвиг назад.
            // +26 и %26 обеспечивают переход через границу алфавита.
            const nyIndeks = (indeks + retning * nokkel + 26) % 26;
            const nyBokstav = alfabet.charAt(nyIndeks);

            // Сохраняем большие и маленькие буквы
            if (tegn === tegn.toLowerCase()) {
                nyTekst += nyBokstav.toLowerCase();
            } else {
                nyTekst += nyBokstav;
            }
        }

        resultat = nyTekst;
    }
</script>

<main>
    <h1>Kryptografiverktøy</h1>
    <h2>Caesar-kryptering</h2>
    <p>Skriv inn engelske bokstaver og en nøkkel fra 0 til 25.</p>

    <label for="tekst">Tekst:</label>
    <textarea
        id="tekst"
        bind:value={tekst}
        rows="4"
        placeholder="Hello world"
    ></textarea>

    <label for="nokkel">Nøkkel (0–25):</label>
    <input
        id="nokkel"
        type="number"
        min="0"
        max="25"
        step="1"
        bind:value={nokkel}
    />

    <div class="knapper">
        <button onclick={() => caesar(1)}>Krypter</button>
        <button onclick={() => caesar(-1)}>Dekrypter</button>
    </div>

    {#if feil}
        <p class="feil" role="alert">{feil}</p>
    {/if}

    <h2>Resultat</h2>
    <div class="resultat" aria-live="polite">{resultat}</div>
</main>

<style>
    :global(body) {
        margin: 0;
        background: #eef2f6;
        font-family: Arial, sans-serif;
        color: #222;
    }

    main {
        max-width: 600px;
        margin: 30px auto;
        padding: 24px;
        background: white;
        border-radius: 10px;
    }

    h1 {
        margin-top: 0;
    }

    label {
        display: block;
        margin-top: 16px;
        margin-bottom: 6px;
        font-weight: bold;
    }

    textarea,
    input {
        box-sizing: border-box;
        width: 100%;
        padding: 10px;
        border: 1px solid #888;
        border-radius: 5px;
        font: inherit;
    }

    textarea {
        resize: vertical;
    }

    .knapper {
        display: flex;
        flex-wrap: wrap;
        gap: 10px;
        margin-top: 16px;
    }

    button {
        padding: 10px 20px;
        background: #245db5;
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font: inherit;
    }

    button:hover {
        background: #194589;
    }

    .feil {
        color: #b00020;
    }

    .resultat {
        min-height: 24px;
        padding: 12px;
        background: #eef2f6;
        border-radius: 5px;
        white-space: pre-wrap;
        overflow-wrap: anywhere;
    }
</style>