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
            total: 9
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
                    //magic
                    let num1 = aLength < 0 ? 0 : a[aLength] | 0; // OR
                    let num2 = bLength < 0 ? 0 : b[bLength] | 0;
                    carry += num1 + num2; // sum of the two single digits
                    result = carry % 2 + result; //concat string in proper order
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
                this.log.push("Initiation --> Rg3:=" + this.reg3);
                this.log.push("y1 --> L:=" + this.total);

                for (; this.counter > 0;) {
                    // x1
                    const addToSum = this.reg2[this.reg2.length - 1] === "1"
                    this.log.push("x1 --> " + addToSum);

                    if (addToSum) {
                        // summa - y2
                        this.reg3 = this.sumBinary(this.reg1, this.reg3)
                        this.log.push("y2 --> Rg3:=" + this.reg3);
                    }
                    this.counter--; // y3
                    this.log.push("y3 --> L:=" + this.counter);

                    this.reg2 = this.toBinary(this.toDecimal(this.reg2) >> 1); // y4
                    this.log.push("y4 --> Rg2:=" + this.reg2);

                    this.reg1 = this.toBinary(this.toDecimal(this.reg1) << 1); //y5
                    this.log.push("y5 --> Rg1:=" + this.reg1);
                }

                this.log.push("x2 --> 1: Programm lõpetatud. Vastus " + this.reg3)
            },
            toBinary(val) {
                let res = (val >>> 0).toString(2);
                return res.length > this.total ? res.slice(res.length - this.total) : this.addPadding(res);
            },
            toDecimal(val) {
                return parseInt(val, 2);
            }
        }
    }
</script>

<style>

</style>
