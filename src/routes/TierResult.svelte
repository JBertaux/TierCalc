<script lang="ts">
    let { tierCredits } = $props();

    type MembershipLevel = 'GREEN' | 'SILVER' | 'PLATINUM' | 'CONCIERGE';
    type FareEurope = 'SAVER' | 'PLUS' | 'ADVANTAGE' | 'AERSPACE';
    type FareTransatlantic = 'SAVER' | 'SMART' | 'FLEX' | 'BUSINESS' | 'BUSSINESSFLEX';

    type Fare = {
        key: FareEurope | FareTransatlantic;
        label: string;
        emoji: string;
    }

    const fares: Record<FareEurope | FareTransatlantic, Fare> = {
        SAVER: { key: 'SAVER', label: 'Saver', emoji: '💸' },
        PLUS: { key: 'PLUS', label: 'Plus', emoji: '💸' },
        ADVANTAGE: { key: 'ADVANTAGE', label: 'Advantage', emoji: '💸' },
        AERSPACE: { key: 'AERSPACE', label: 'Aer Space', emoji: '💸' },
        SMART: { key: 'SMART', label: 'Smart', emoji: '💸' },
        FLEX: { key: 'FLEX', label: 'Flex', emoji: '💸' },
        BUSINESS: { key: 'BUSINESS', label: 'Business', emoji: '💸' },
        BUSSINESSFLEX: { key: 'BUSSINESSFLEX', label: 'Business Flex', emoji: '💸' }
    };

    let requiredCredits: Record<MembershipLevel, number> = {
        GREEN: 0,
        SILVER: 301,
        PLATINUM: 601,
        CONCIERGE: 1051
    };

    let pointPerFlightEurope: Record<MembershipLevel, Record<FareEurope, number>> = {
        GREEN: {
            SAVER: 0,
            PLUS: 0,
            ADVANTAGE: 0,
            AERSPACE: 0
        },
        SILVER: {
            SAVER: requiredCredits.SILVER / 21,
            PLUS: requiredCredits.SILVER / 13,
            ADVANTAGE: requiredCredits.SILVER / 8,
            AERSPACE: requiredCredits.SILVER / 7
        },
        PLATINUM: {
            SAVER: requiredCredits.PLATINUM / 41,
            PLUS: requiredCredits.PLATINUM / 25,
            ADVANTAGE: requiredCredits.PLATINUM / 15,
            AERSPACE: requiredCredits.PLATINUM / 13
        },
        CONCIERGE: {
            SAVER: requiredCredits.CONCIERGE / 71,
            PLUS: requiredCredits.CONCIERGE / 43,
            ADVANTAGE: requiredCredits.CONCIERGE / 26,
            AERSPACE: requiredCredits.CONCIERGE / 22
        }
    };

    let pointPerFlightTransatlantic: Record<MembershipLevel, Record<FareTransatlantic, number>> = {
        GREEN: {
            SAVER: 0,
            SMART: 0,
            FLEX: 0,
            BUSINESS: 0,
            BUSSINESSFLEX: 0
        },
        SILVER: {
            SAVER: requiredCredits.SILVER / 21,
            SMART: requiredCredits.SILVER / 7,
            FLEX: requiredCredits.SILVER / 5,
            BUSINESS: requiredCredits.SILVER / 3,
            BUSSINESSFLEX: requiredCredits.SILVER / 2
        },
        PLATINUM: {
            SAVER: requiredCredits.PLATINUM / 41,
            SMART: requiredCredits.PLATINUM / 13,
            FLEX: requiredCredits.PLATINUM / 9,
            BUSINESS: requiredCredits.PLATINUM / 5,
            BUSSINESSFLEX: requiredCredits.PLATINUM / 4
        },
        CONCIERGE: {
            SAVER: requiredCredits.CONCIERGE / 71,
            SMART: requiredCredits.CONCIERGE / 22,
            FLEX: requiredCredits.CONCIERGE / 15,
            BUSINESS: requiredCredits.CONCIERGE / 9,
            BUSSINESSFLEX: requiredCredits.CONCIERGE / 7
        }
    };

    function getTier(tierCredits: number): MembershipLevel {
        if (tierCredits >= requiredCredits.CONCIERGE) {
            return 'CONCIERGE';
        } else if (tierCredits >= requiredCredits.PLATINUM) {
            return 'PLATINUM';
        } else if (tierCredits >= requiredCredits.SILVER) {
            return 'SILVER';
        } else {
            return 'GREEN';
        }
    }

    function getNextTier(tierCredits: number): MembershipLevel {
        if (tierCredits < requiredCredits.SILVER) {
            return 'SILVER';
        } else if (tierCredits < requiredCredits.PLATINUM) {
            return 'PLATINUM';
        } else if (tierCredits < requiredCredits.CONCIERGE) {
            return 'CONCIERGE';
        } else {
            return 'CONCIERGE'; // Already at the highest tier
        }
    }

    function calculateFlights(
        pointsPerFlight: Record<FareEurope, number> | Record<FareTransatlantic, number>,
        currentCredits: number,
        requiredCredits: number
    ) : Record<string, number> {
        return Object.fromEntries(
            Object.entries(pointsPerFlight).map(([fare, points]) => [
                fare,
                Math.max(Math.ceil((requiredCredits - currentCredits) / points), 0)
            ])
        );
    }

    let userTier = getTier(tierCredits);
    let nextTier = getNextTier(tierCredits);

    let flightsToNextTierEurope = calculateFlights(
        pointPerFlightEurope[nextTier],
        tierCredits,
        requiredCredits[nextTier]
    );
    let flightsToNextTierTransatlantic = calculateFlights(
        pointPerFlightTransatlantic[nextTier],
        tierCredits,
        requiredCredits[nextTier]
    );
</script>

<div class="p-4">
    <div class="mb-4 text-xl">
        With <span class="font-mono bg-yellow-200 px-2">{tierCredits}</span> tier credits you are currently at the tier <b>{userTier}</b>, your next tier is <b>{nextTier}</b>
    </div>

    <div class="flex flex-wrap">
        <div class="w-full md:w-1/2 p-2">
            <h2 class="mb-2 text-lg font-semibold">🇪🇺 European flights needed to reach next tier level</h2>
            <table class="min-w-full border border-gray-200 bg-white">
                <thead>
                    <tr>
                        <th class="border-b px-4 py-2">Fare 💸</th>
                        <th class="border-b px-4 py-2 plane">Flights Needed</th>
                    </tr>
                </thead>
                <tbody>
                    {#each Object.entries(flightsToNextTierEurope) as [fare, flights]}
                        {@const fareDetails = fares[fare as FareEurope]}
                        <tr>
                            <td class="border-b px-4 py-2">{fareDetails.label} {fareDetails.emoji}</td>
                            <td class="border-b px-4 py-2">{flights}</td>
                        </tr>
                    {/each}
                </tbody>
            </table>
        </div>

        <div class="w-full md:w-1/2 p-2">
            <h2 class="mb-2 text-lg font-semibold">🌎 Transatlantic flights needed to reach next tier level</h2>
            <table class="min-w-full border border-gray-200 bg-white">
                <thead>
                    <tr>
                        <th class="border-b px-4 py-2">Fare 💸</th>
                        <th class="border-b px-4 py-2 plane">Flights Needed</th>
                    </tr>
                </thead>
                <tbody>
                    {#each Object.entries(flightsToNextTierTransatlantic) as [fare, flights]}
                        {@const fareDetails = fares[fare as FareEurope]}
                        <tr>
                            <td class="border-b px-4 py-2">{fareDetails.label} {fareDetails.emoji}</td>
                            <td class="border-b px-4 py-2">{flights}</td>
                        </tr>
                    {/each}
                </tbody>
            </table>
        </div>
    </div>
</div>

<style>
    .plane::after {
        content: ' \2708\FE0F';
    }
</style>
