<template>
    <v-layout>
        <v-flex xs12 sm12 offset-sm1>
            <v-card
                    dark
                    raised
                    max-width="90%">
                <v-card-title>
                    <binary-inputs
                            :reg1="reg1"
                            :reg2="reg2"
                            @setRegister1="updateReg1"
                            @setRegister2="updateReg2"
                            :calculate="calculate"
                            :addPadding="addPadding"
                            :toBinary="toBinary"/>
                </v-card-title>
                <v-card-actions>
                    <logger :log="log"/>
                </v-card-actions>
            </v-card>
        </v-flex>
    </v-layout>
</template>

<script>
    import BinaryInputs from "../components/BinaryInputs"
    import Logger from "../components/Logger"

    export default {
        components: {
            BinaryInputs,
            Logger
        },
        data: () => ({
            reg1: "",
            reg2: "",
            reg3: "",
            counter: 0,
            log: [],
            total: 9,
            M: ""       // Summa viimase biti kontrollimiseks, kas on ületäitumine või ei
        }),
        methods: {
            sumBinary(a, b) {
                let carry = 0;
                let result = "";
                // itereeri kuni 1 pikkus on joudnud uhe binaari pikkuseni
                let aLength = a.length - 1;
                let bLength = b.length - 1;
                // liida ja anna ulekanne edasi
                while (aLength >= 0 || bLength >= 0) {
                    // kui ühes registris saavad bitid otsa, määra liidetavaks 0
                    let num1 = aLength < 0 ? 0 : a[aLength] | 0; // OR
                    let num2 = bLength < 0 ? 0 : b[bLength] | 0;
                    carry += num1 + num2; // kahe biti liitmine
                    result = carry % 2 + result; // summa kokku panemine
                    carry = carry / 2 | 0; // remove fractionals
                    aLength--;
                    bLength--;
                }

                if (carry) {
                    result = carry + result;
                }
                return result.slice(result.length - this.total);
            },
            addPadding(bin) {
                return "0".repeat(this.total - bin.length) + bin
            },
            updateReg1(val) {
                this.reg1 = val
            },
            updateReg2(val) {
                this.reg2 = val
            },
            calculate() {
                this.log = [];
                this.counter = this.total;
                this.reg3 = "0".repeat(this.total)
                this.startCycles()
            },
            startCycles() {
                this.log.push("Logi on kujul 'operatsioon --> tulemus'");
                this.log.push("-----------------------------------------------------------------");
                this.log.push("Algväärtustamine --> Rg1:=" + this.reg1);
                this.log.push("Algväärtustamine --> Rg2:=" + this.reg2);
                this.log.push("Algväärtustamine --> Rg3:=" + this.reg3);
                this.log.push("y1 --> L:=" + this.total);

                let errorOccurred = false

                for (; this.counter > 0;) {
                    this.log.push("---------- TSÜKKEL ALGAB ----------")
                    // x1
                    const addToSum = this.reg2[this.reg2.length - 1] === "1"
                    this.log.push("x1 --> " + (addToSum ? "1" : "0"));

                    if (addToSum) {
                        // jäta summa viimane bit meelde
                        this.M = this.reg3[0]

                        // summa - y3
                        this.reg3 = this.sumBinary(this.reg1, this.reg3)
                        this.log.push("y3 --> Rg3:=" + this.reg3);

                        // Ületäitumise kontroll
                        if ((this.M | 0) && !(this.reg3[0] | 0)) {
                            this.log.push("x2 --> Tekkis ületäitumine ja programm lõpetatakse! Viimane vahesumma on: 1" + this.reg3)
                            errorOccurred = true
                            break;
                        }
                    }
                    this.counter--; // y4
                    this.log.push("y4 --> L:=" + this.counter);

                    this.reg2 = this.toBinary(this.toDecimal(this.reg2) >> 1); // y5
                    this.log.push("y5 --> Rg2:=" + this.reg2);

                    this.reg1 = this.toBinary(this.toDecimal(this.reg1) << 1); //y6
                    this.log.push("y6 --> Rg1:=" + this.reg1);
                    if (this.counter !== 1) this.log.push("x3 --> 0")
                }

                if (!errorOccurred) this.log.push("x3 --> 1, Programm lõpetatud. Vastus " + this.reg3)
            },
            toBinary(val) {
                let res = (val >>> 0).toString(2);
                return res.length > this.total ? res.slice(res.length - this.total) : this.addPadding(res);
            },
            toDecimal(val) {
                if (val[0] === "1") {
                    let result = ""
                    let oneFound = false

                    for (let i = val.length - 1; i  > 0; i--) {
                        if (!oneFound) result = val[i] + result
                        if (oneFound) result = +!(val[i] % 2) + result
                        if (val[i] === "1") oneFound = true
                    }
                    return parseInt(result.slice(1), 2) * -1
                }
                return parseInt(val, 2);
            }
        }
    }
</script>

<style>

</style>
