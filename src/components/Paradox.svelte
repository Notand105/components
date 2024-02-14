<script lang="ts">
    import type { Door } from "../components/types";
    let doors: Door[] = [];
    let selected = 0;
    let picked = false;
    let win: string = "";
    let quant = 3;
    let played = 0;
    let won = 0;
    let limit = 1000;
    let simulate = false;
    fillDoors(quant);
    generatePrize();

    $: reset(quant);

    function fillDoors(max: number) {
        doors = [];
        for (let i = 1; i <= max; i++) {
            let door: Door = {
                id: i,
                selected: false,
                prize: false,
                open: false,
                option: false,
            };
            door.id = i;
            doors = [...doors, door];
        }
    }

    function generatePrize() {
        let ranIndex = Math.floor(Math.random() * doors.length);
        console.log(ranIndex);
        if (doors[ranIndex]) {
            doors[ranIndex].prize = true;
        }
    }
    function selectDoor(index: number) {
        if (picked) return;
        //we delete last selected door
        selected = index;
        for (let i = 1; i <= doors.length; i++) {
            doors[i - 1].selected = false;
        }
        //then select the one
        for (let i = 1; i <= doors.length; i++) {
            if (index == i) {
                doors[i - 1].selected = true;
            }
        }
    }

    function pickDoor() {
        //open every non-selected and non-prize door if the one selected has the prize leave one closed
        picked = true;
        let aux: number = -1;
        if (doors[selected - 1].prize) {
            do aux = Math.floor(Math.random() * doors.length);
            while (aux == selected - 1);
        }
        for (let i = 0; i < doors.length; i++) {
            if (!doors[i].prize && !doors[i].selected && !(i == aux)) {
                doors[i].open = true;
            }
        }
    }

    function changeOption() {
        for (let i = 1; i <= doors.length; i++) {
            if (!doors[i - 1].selected && !doors[i - 1].open) {
                doors[selected - 1].selected = false;
                doors[i - 1].selected = true;
                selected = i;
                break;
            }
        }
        checkWin();
    }

    function checkWin() {
        played++;
        let aux = doors[selected - 1].prize;
        if (aux) {
            win = "You Won!";
            won++;
        } else {
            win = "You Lost";
        }
        openAll();
    }

    function reset(amount: number) {
        doors = [];
        selected = 0;
        picked = false;
        win = "";

        fillDoors(amount);
        generatePrize();
    }

    function openAll() {
        for (let i = 0; i < doors.length; i++) {
            doors[i].open = true;
        }
    }

    function autoPlay(keep: boolean) {
        for (let i = 0; i < limit; i++) {
            if (keep) {
                selectDoor(1);
                pickDoor();
                checkWin();
                reset(quant)
            } else {
                selectDoor(1);
                pickDoor();
                changeOption();
                reset(quant);
            }

        }
    }
    function toggleMode(boo:boolean){
        simulate = boo 
        reset(quant)
    }

    function resetScore() {
        played = 0;
        won = 0;
    }
</script>

<section class="">
    <h1>Monty Hall problem</h1>
    <div class="game elec">
        <div class="cont">
            {#each doors as doo}
                <div class="wrapper" class:selected={doo.selected}>
                    <button
                        class="door doora"
                        class:open={doo.open}
                        on:click={() => selectDoor(doo.id)}
                    >
                        <span class="doorframe">
                            {doo.id}
                        </span>
                    </button>
                    <div class="back">
                        {#if doo.prize}
                            <img src="money.svg" alt="prize" />
                        {:else}
                            <img src="/trash.svg" alt="empty" />
                        {/if}
                    </div>
                </div>
            {/each}
        </div>
        <div class="al">
            <div>
                <button disabled={!simulate} on:click={()=>toggleMode(false)}
                    >Play</button
                >
                <button disabled={simulate} on:click={()=>toggleMode(true)}
                    >Simulate</button
                >
            </div>
            <div class="ooo">
                {#if simulate}
                    <div class="simulate">
                        <button on:click={() => autoPlay(true)}
                            >AutoPlay keeping door</button
                        >
                        <button on:click={() => autoPlay(false)}
                            >AutoPlay changing door</button
                        >
                    </div>
                {:else}
                    <div class="controls">
                        {#if !selected}
                            <h3>Select a door</h3>
                        {/if}
                        {#if selected != 0 && !picked}
                            <h3>You selected door number {selected}</h3>
                            <button on:click={pickDoor}>confirm</button>
                        {/if}
                        {#if picked && win === ""}
                            <h3>You selected door number {selected}</h3>
                            <button on:click={changeOption}>
                                change door
                            </button>
                            <button on:click={checkWin}>
                                keep your choice
                            </button>
                        {/if}
                        {#if win !== ""}
                            <h3>You selected door number {selected}</h3>
                            {win}
                            <button on:click={() => reset(quant)}
                                >Play again</button
                            >
                        {/if}
                    </div>
                {/if}
            </div>
        </div>
    </div>
    <div class="misc">
        <div style="display:flex;flex-direction:column">
            <span>won games: {won}</span>
            <span>played games: {played}</span>

            {#if won / played}
                <span>rate: {100 * (won / played)}%</span>
            {:else}
                <span>rate: 0%</span>
            {/if}
            <span>
                amount of doors
                <input type="number" bind:value={quant} />
            </span>
            <button style="width:200px;" on:click={resetScore}
                >Reset score</button
            >
        </div>
    </div>
</section>

<style>
    *{
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
    .misc {
        width: 100%;
        justify-content: center;
        display: flex;
        align-items: center;
        flex-direction: column;
    }
    img {
        width: 100%;
    }
    .al{
        width: 100%;
        padding: 0rem 3rem 3rem 1rem;
    }
    .wrapper {
        position: relative;
        display: flex;
    }
    .ooo {
        justify-content: center;
        display: flex;
        align-items: center;
        flex-direction: column;
        margin: auto;
        background-color: #242424;
        height: 200px;
    }
    .door {
        height: 200px;
        flex: 1;
        min-width: 120px;
        max-width: 200px;
        display: flex;
        justify-content: center;
        align-items: center;
        padding: 1rem;
        position: relative;
        background-color: #cccccc;
    }
    .elec {
        display: flex;
        flex-direction: column;
    }
    .back {
        position: absolute;
        width: 100%;
        height: 100%;
        top: 0;
        left: 0;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        z-index: -1;
    }
    .cont {
        display: flex;
        flex-wrap: wrap;
        gap: 1rem;
        justify-content: center;
        align-items: center;
        width: 100%;
    }
    .selected {
        border: 5px solid rgb(240, 160, 160);
        box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
    }
    .game {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        gap: 4rem;
        width: 90%;
        margin: auto;
    }
    .open {
        perspective: 1000px;
        transform-style: preserve-3d;
        transform: rotateY(-75deg);
        transition: 0.6s ease;
        transform-origin: left;
    }
    .controls {
        min-width: 30%;
        right: 0;
        top: 30%;
        z-index: 10;
        padding: 1rem;
    }
</style>
