<script>
    import { replace } from 'svelte-spa-router';
	// activity-store data objects and function
    import {
        displaySettings, 
        currentDotSettings, 
        dotCount, 
        generateRandCoordinates, 
        currentMapSettings
    } from '../stores/activity-store.js';
    // console.clear();
    // console.log("curGen: " + $currentMapSettings.currentGeneration);
    // console.log("maxGen: " + $currentMapSettings.maxGeneration);
    // add current generation's dot count to the survival data
    $currentMapSettings.survivalData[$currentMapSettings.currentGeneration] = $dotCount;
    // console.table($currentMapSettings.survivalData);
    // console.log("reproduce: " + $currentMapSettings.reproduce);

    // console.log($currentMapSettings.currentGeneration);
    // console.log("multiplier: " + $currentMapSettings.genMultiplier);
    // Continue to next generation (or final results)
    // TODO: Revise to display "next generation" and "final results" button and break continueActivity() into similarly-named functions
    function continueActivity() {
        if ($currentMapSettings.currentGeneration < $currentMapSettings.maxGeneration) {
            $currentMapSettings.currentGeneration = $currentMapSettings.currentGeneration + 1;
            let newRandCoords = [];
            // console.log($dotCount);
            if($currentMapSettings.margins) {
                $displaySettings.marginParams 
                    = $currentMapSettings.margins[$currentMapSettings.currentGeneration];
            }
            if ($currentMapSettings.reproduce) {
                for (let item in $dotCount) {
                // console.log(item, $dotCount[item]);
                newRandCoords = newRandCoords.concat(generateRandCoordinates(
                    $displaySettings.height,
                    $displaySettings.width,
                    $displaySettings.marginParams,
                    (parseInt($dotCount[item]) * ($currentMapSettings.genMultiplier)),
                    [item]
                ));
                }
                $currentDotSettings.randomCoordinates = [...newRandCoords];
            }
            // console.table($currentDotSettings.randomCoordinates);
            // console.table($currentMapSettings);
            replace("/" + $currentMapSettings.mapRoute);
        } else {
            replace("/finalResults");
        }
    }
</script>

<main>
    <h1>RESULTS</h1>
    <p>{$currentMapSettings.currentGeneration} of {$currentMapSettings.maxGeneration} generations completed</p>
    <ul>
        {#each $currentDotSettings.dotColors as aColor, index}
            <li>
                <span style="color: {aColor}; font-weight: bold">{aColor.toUpperCase()}:&nbsp;</span>{$dotCount[aColor]}
            </li>
        {/each}
    </ul>
    <form>
        {#if (($currentMapSettings.currentGeneration) < $currentMapSettings.maxGeneration)}
            <button on:click|preventDefault="{continueActivity}">Next Generation</button>
        {:else}
            <button on:click|preventDefault="{continueActivity}">Final Results</button>
        {/if}
    </form>
</main>

<style>
     button {
		background-color: #4CAF50; /* Green */
		border: 1px solid black;
		color: white;
		padding: 8px 12px;
		text-align: center;
		text-decoration: none;
		font-weight: bolder;
		display: inline-block;
		font-size: 20px;
	}
</style>