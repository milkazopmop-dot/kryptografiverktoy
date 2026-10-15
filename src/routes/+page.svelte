<script lang="ts">
    let metode = $state("Caesar");
    let tekst = $state("");
    let nokkel = $state<number | undefined>(3);
    let vigenereNokkel = $state("");
    let resultat = $state("");
    let feil = $state("");

    const alfabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
    const bokstaver = alfabet + alfabet.toLowerCase();

    function byttMetode() {
        resultat = "";
        feil = "";
    }

    function kontrollerInput() {
        if (!tekst.trim()) {
            feil = "Skriv inn en tekst.";
            return false;
        }

        // Разрешены только английские буквы и обычные пробелы
        for (const tegn of tekst) {
            if (!bokstaver.includes(tegn) && tegn !== " ") {
                feil = "Bruk bare engelske bokstaver og mellomrom. Ikke bruk Enter.";
                return false;
            }
        }

        if (metode === "Caesar") {
            if (nokkel === undefined) {
                feil = "Skriv inn en nøkkel.";
                return false;
            }

            if (!Number.isInteger(nokkel) || nokkel < 0 || nokkel > 25) {
                feil = "Nøkkelen må være et heltall fra 0 til 25.";
                return false;
            }
        } else {
            if (!vigenereNokkel) {
                feil = "Skriv inn en Vigenère-nøkkel.";
                return false;
            }

            // В ключе Vigenère разрешены только английские буквы
            for (const tegn of vigenereNokkel) {
                if (!bokstaver.includes(tegn)) {
                    feil = "Nøkkelen kan bare inneholde engelske bokstaver.";
                    return false;
                }
            }
        }

        return true;
    }

    function flyttBokstav(tegn: string, forskyvning: number) {
        const indeks = alfabet.indexOf(tegn.toUpperCase());

        // +26 и %26 позволяют перейти через границу алфавита
        const nyIndeks = (indeks + forskyvning + 26) % 26;
        const nyBokstav = alfabet.charAt(nyIndeks);

        // Сохраняем регистр исходной буквы
        return tegn === tegn.toLowerCase()
            ? nyBokstav.toLowerCase()
            : nyBokstav;
    }

    function caesar(retning: number, valgtNokkel: number) {
        let nyTekst = "";

        for (const tegn of tekst) {
            if (tegn === " ") {
                nyTekst += " ";
            } else {
                nyTekst += flyttBokstav(tegn, retning * valgtNokkel);
            }
        }

        return nyTekst;
    }

    function vigenere(retning: number) {
        let nyTekst = "";
        let nokkelIndeks = 0;
        const storNokkel = vigenereNokkel.toUpperCase();

        for (const tegn of tekst) {
            // Пробел сохраняем, букву ключа на него не расходуем
            if (tegn === " ") {
                nyTekst += " ";
                continue;
            }

            // Повторяем ключ, когда доходим до его конца
            const nokkelBokstav = storNokkel.charAt(
                nokkelIndeks % storNokkel.length
            );

            // A — сдвиг 0, B — 1, C — 2 и так далее
            const forskyvning = alfabet.indexOf(nokkelBokstav);

            nyTekst += flyttBokstav(tegn, retning * forskyvning);
            nokkelIndeks++;
        }

        return nyTekst;
    }

    function behandle(retning: number) {
        feil = "";
        resultat = "";

        if (!kontrollerInput()) {
            return;
        }

        // 1 — шифрование, -1 — расшифровка
        if (metode === "Caesar") {
            if (nokkel !== undefined) {
                resultat = caesar(retning, nokkel);
            }
        } else {
            resultat = vigenere(retning);
        }
    }
</script>

<main>
    <h1>Kryptografiverktøy</h1>
    <p>Krypter og dekrypter tekst med Caesar eller Vigenère.</p>

    <label for="metode">Metode:</label>
    <select
        id="metode"
        bind:value={metode}
        onchange={byttMetode}
    >
        <option value="Caesar">Caesar</option>
        <option value="Vigenere">Vigenère</option>
    </select>

    <label for="tekst">Tekst:</label>
    <textarea
        id="tekst"
        bind:value={tekst}
        rows="4"
        placeholder="Hello world"
    ></textarea>

    {#if metode === "Caesar"}
        <label for="nokkel">Nøkkel (0–25):</label>
        <input
            id="nokkel"
            type="number"
            min="0"
            max="25"
            step="1"
            bind:value={nokkel}
        />
    {:else}
        <label for="vigenereNokkel">Nøkkel (engelske bokstaver):</label>
        <input
            id="vigenereNokkel"
            type="text"
            bind:value={vigenereNokkel}
            placeholder="KEY"
        />
    {/if}

    <div class="knapper">
        <button onclick={() => behandle(1)}>Krypter</button>
        <button onclick={() => behandle(-1)}>Dekrypter</button>
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
    input,
    select {
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