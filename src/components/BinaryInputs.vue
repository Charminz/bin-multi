<template>
    <v-layout>
        <v-flex xs12 sm12 >
            <v-card flat color="transparent">
                <v-card-text>
                    <v-layout row wrap>
                        <v-flex xs12 sm12 md12 class="mb-4">
                            <h2>Suvalise märgiga arvude vahetu korrutamine</h2>
                            <div class="body-2">(9-järgulised märgiga täisarvud, negatiivsed operandid täiendkoodis)</div>
                        </v-flex>
                        <v-flex xs12 sm6 md6 class="mr-4">
                            <v-text-field
                                    v-model="a"
                                    :rules="binaryCheck"
                                    maxlength="9"
                                    label="A (Rg2)"
                                    placeholder=" "
                            ></v-text-field>
                        </v-flex>
                        <v-flex xs12 sm6 md6>
                            <v-text-field
                                    v-model="b"
                                    :rules="binaryCheck"
                                    maxlength="9"
                                    label="B (Rg1)"
                                    placeholder=" "
                            ></v-text-field>
                        </v-flex>
                    </v-layout>
                </v-card-text>

                <v-card-actions>
                    <v-btn color="#3BB083" @click="setAndCalc">Arvuta</v-btn>
                    <v-btn color="#98B8DD" @click="setFirstPreset">32 x -7</v-btn>
                    <v-btn color="#98B8DD" @click="setSecondPreset">-16 x 13</v-btn>
                </v-card-actions>
            </v-card>
        </v-flex>
    </v-layout>
</template>

<script>
    export default {
        props: {
            reg1: {type: [String, Number], required: true},
            reg2: {type: [String, Number], required: true},
            calculate: {type: Function, required: true},
            toBinary: {type: Function, required: true}
        },
        data: () => ({
            a: "",
            b: "",
            binaryCheck: [
                (value) => {
                    return /^[01]+$/.test(value) || "Sisesta kahendarv!"
                }
            ]
        }),
        methods: {
            setAndCalc() {
                this.$emit("setRegister1", this.b);
                this.$emit("setRegister2", this.a);
                this.calculate()
            },
            setFirstPreset() {
                this.b = this.toBinary(-7);
                this.a = this.toBinary(32);
                this.setAndCalc()
            },
            setSecondPreset() {
                this.b = this.toBinary(13);
                this.a = this.toBinary(-16);
                this.setAndCalc()
            }
        }
    }
</script>