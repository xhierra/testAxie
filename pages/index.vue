<template>
    <div>
      
        <v-container>
                    <div class="d-flex flex-row">

                        <v-select
                        v-model="auctionSearch"
                        class="priceField mx-3"
                        :items="sortbyauction"
                        item-text="name"
                        item-value="val"
                        label="Auction Type"
                        filled
                        rounded
                        :disabled="axie.length == 0"
                        ></v-select>

                        <v-select
                        v-model="priceSearch"
                        class="priceField mx-3"
                        :items="sortbyprice"
                        item-text="name"
                        item-value="val"
                        label="Price"
                        filled
                        rounded
                        :disabled="axie.length == 0"
                        ></v-select>
                        
                    </div>
            <h1 v-if="axie.length != 0">{{axie.axies.total}} Axies</h1>
        </v-container>
        <Axie v-if="axie.length != 0" :axielist="axie" :price="price"/>
        <Loads v-if="axie.length == 0" style="margin-top:100px" />

        <v-pagination
        class="mb-5 mt-5"
        v-if="pagination != 0"
        v-model="currentPage"
        :length="pagination"
        :total-visible="4"
        
        ></v-pagination>
        



        <!-- Classes -->
        <v-bottom-sheet
        v-model="class_search"
        inset
        >
        <v-card
            class="text-center"
            height="200px"
            shaped
            color="#11131B"
        >

            <v-btn
            class="mt-6"
            text
            color="error"
            @click="class_search = !class_search"
            >
            close
            </v-btn>

            <v-container>

                <span class="pa-2" v-for="icon in selected" :key="icon"  :inner-html.prop="icon|classaxie"></span>

                <v-select
                v-model="selected"
                :items="items"
                multiple
                chips
                filled
                rounded
                >
                    <template v-slot:selection="data">
                        <v-chip
                        :key="JSON.stringify(data.item)"
                        v-bind="data.attrs"
                        :input-value="data.selected"
                        :disabled="data.disabled"
                        @click:close="data.parent.selectItem(data.item)"
                        :color="classaxie(data.item)"
                        >
                        <v-avatar
                            color="white"
                            left
                        >
                        <span :inner-html.prop="data.item|classaxie"></span>
                        </v-avatar>
                            <span class="white--text">{{ data.item }}</span>
                        </v-chip>
                    </template>
                </v-select>

            </v-container>


        </v-card>
        </v-bottom-sheet>
        <!-- Classes -->

        <!-- Stages -->
        <v-bottom-sheet
        v-model="stage_search"
        inset
        >
        <v-card
            class="text-center"
            height="250px"
            shaped
            color="#11131B"
        >

            <v-btn
            class="mt-6"
            text
            color="error"
            @click="stage_search = !stage_search"
            >
            close
            </v-btn>

            <v-container>
                <v-select
                label="Stage"
                v-model="selected_stage"
                :items="items_stage"
                item-text="name"
                item-value="val"
                multiple
                chips
                filled
                rounded
                >
                </v-select>

                <v-range-slider
                :hint="`${breed_count[0]}` +' max of ' + `${breed_count[1]}` "
                v-model="breed_count"
                :max="max"
                :min="min"
                label="Breed Count"
                persistent-hint
                ></v-range-slider>

            </v-container>


        </v-card>
        </v-bottom-sheet>
        <!-- Stages -->

        <!-- Rarity -->
        <v-bottom-sheet
        v-model="rarity_search"
        inset
        >
        <v-card
            class="text-center"
            height="200px"
            shaped
            color="#11131B"
        >

            <v-btn
            class="mt-6"
            text
            color="error"
            @click="rarity_search = !rarity_search"
            >
            close
            </v-btn>

            <v-container class="pa-5">
                

                <v-slider
                    v-model="mystic"
                    :tick-labels="rarity_labels"
                    label="MYSTIC"
                    :max="6"
                    step="1"
                    ticks="always"
                    tick-size="6"
                ></v-slider>

                <!-- <v-slider
                    v-model="purity"
                    :tick-labels="pureness_labels"
                    label="Pureness"
                    :max="6"
                    step="1"
                    ticks="always"
                    tick-size="6"
                ></v-slider> -->


            </v-container>


        </v-card>
        </v-bottom-sheet>
        <!-- Rarity -->



        <v-bottom-navigation
        app
        horizontal
        style="background-color:#11131B"
        >
            <v-btn @click="class_search = !class_search" value="Class">
            <span>Class</span>

            <v-icon>mdi-fish</v-icon>
            </v-btn>

            <v-btn @click="stage_search = !stage_search" value="Stage">
            <span>Stage</span>

            <v-icon>mdi-egg-easter</v-icon>
            </v-btn>

            <v-btn @click="rarity_search = !rarity_search" value="Rarity">
            <span>Rarity</span>

            <v-icon>mdi-chart-bar</v-icon>
            </v-btn>

            <v-btn @click="clearFilter()" value="Clear">
            <span>Clear Filter</span>

            </v-btn>
        </v-bottom-navigation>
    </div>
</template>

<script>
    import Axie from '~/components/axie/axie.vue'
    import Loads from '~/components/axie/axieload.vue'
    import _ from 'lodash'
    export default {
        components:{
            Axie,
            Loads
        },
        computed:{
            pagination : function (){
                return  this.axie.length != 0 ? Math.round(this.axie.axies.total / this.page) : 0;
            }
        },
        data: () => ({
            Clear: false,
            page:24,
            currentPage:1,
            axie: [],
            price: [],
            selected:[],
            selected_stage: [],
            class_search: false,
            stage_search : false,
            rarity_search :false,
            priceSearch: 'Latest',
            auctionSearch: 'Sale',
            mystic: 0,
            purity:0,
            breed_count: [0,7],
            min: 0,
            max: 7,

            items: [
                'Beast',
                'Plant',
                'Aquatic',
                'Bird',
                'Bug',
                'Dawn',
                'Reptile',
                'Mech',
                'Dusk',
            ],
            
            sortbyprice:[

                {
                    name: "Lowest ID",
                    val: "IdAsc"
                },

                {
                    name: "Highest ID",
                    val: "IdDesc"
                },
                {
                    name: "Lowest Price",
                    val: "PriceAsc"
                },
                {
                    name: "Highest Price",
                    val: "PriceDesc"
                },
                {
                    name: "Latest",
                    val: "Latest"
                }
            ],
            sortbyauction:[

                {
                    name: "All",
                    val: "All"
                },

                {
                    name: "For sale",
                    val: "Sale"
                },
                {
                    name: "Not for sale",
                    val: "NotForSale"
                },
            ],
            items_stage:[
                {
                    name: "Egg",
                    val: 1
                },
                {
                    name: "Adult",
                    val: 4
                }
            ],
            rarity_labels: [
                'Any',
                '1',
                '2',
                '3',
                '4',
                '5',
                '6'
            ],
            pureness_labels: [
                'Any',
                '1',
                '2',
                '3',
                '4',
                '5',
                '6'
            ],
        }),
        filters:{
                classaxie : function (axie){

                        if(axie == "Beast"){
                            return '<svg width="16" height="16" viewBox="0 0 16 16" style="fill: #ba8b1e;"><path d="M7.933 4.886a1.91 1.91 0 100-3.82 1.91 1.91 0 000 3.82M12.713 2.635a1.91 1.91 0 100 3.82 1.91 1.91 0 000-3.82M5.064 4.544a1.91 1.91 0 10-3.82 0 1.91 1.91 0 003.82 0M7.916 6.11a4.487 4.487 0 100 8.972 4.487 4.487 0 000-8.973"></path></svg>';
                        }

                        if(axie == "Plant"){
                            return '<svg width="16" height="16" viewBox="0 0 16 16" style="fill: #6cc000;"><path d="M14.205 4.357c-.796-.634-1.882-.941-2.89-.74C9.764 3.926 8.53 5.31 8 6.757c-.53-1.447-1.764-2.831-3.314-3.14-1.009-.201-2.095.106-2.891.74C.847 5.112.5 6.291.836 7.45c.255.879 1.11 1.204 1.933 1.364.912.178 1.906.33 2.617.997a4.745 4.745 0 011.233 1.946c.073.218.137.44.19.665.049.203.056.415.096.62.036.19.112.106.19.01.392-.485.692-1.08.905-1.696.213.616.513 1.21.905 1.695.078.097.154.18.19-.01.04-.204.048-.416.096-.619.053-.224.117-.447.19-.665a4.745 4.745 0 011.234-1.946c.71-.666 1.703-.82 2.616-.997.823-.16 1.678-.485 1.933-1.364.335-1.16-.011-2.338-.959-3.093"></path></svg>';
                        }

                        if(axie == "Aquatic"){
                            return '<svg width="16" height="16" viewBox="0 0 16 16" style="fill: #00b8ce;"><path d="M15.036 5.73c-.136-.615-.329-1.207-.989-.985-.3.102-.578.285-.843.47a8.114 8.114 0 00-1.82 1.777c-.646-1.22-1.717-2.15-2.73-2.786-1.575-.99-3.155-1.12-4.78-.239C2.5 4.712 1.326 6.03.94 7.717a2.81 2.81 0 00-.051.304c.012.1.027.202.05.304.387 1.686 1.562 3.005 2.935 3.75 1.625.88 3.205.751 4.78-.24 1.013-.636 2.084-1.565 2.73-2.786a8.108 8.108 0 001.82 1.776c.265.186.542.37.843.471.66.222.853-.369.989-.985.165-.747.21-1.522.189-2.29.02-.768-.024-1.543-.19-2.29"></path></svg>'
                        }

                        if(axie == "Bird"){
                            return '<svg width="16" height="16" viewBox="0 0 16 16" style="fill: #503d52;"><path d="M9.745 12.248a4.564 4.564 0 01-1.948.837c-.424.076-.857.046-1.278-.036-.386-.074-.86-.148-1.193-.36-.142-.089-.588.97-.645 1.075-.301.554-.783 1-1.357 1.238-.026.011-.056.021-.081.01-.029-.014-.038-.05-.043-.082-.088-.57.1-1.144.433-1.606.085-.117 1.03-1.065.876-1.168-.328-.221-.586-.626-.809-.949-.244-.353-.445-.737-.546-1.157a4.562 4.562 0 01-.014-2.12c.061-.286.246-.661.499-.824.068-.043.487.468.533.519.277.307.504.588.86.818L4.387 6.78c-.04-.102-.08-.212-.058-.32a.579.579 0 01.123-.222c1.52-1.988 3.078-4.134 5.606-4.845.189-.053 2.254-.687 2.36-.452.556 1.222.73 2.593.585 3.923-.128 1.167-.61 2.34-1.004 3.407-.275.743-.846 1.401-1.23 2.096a.58.58 0 01-.154.202c-.09.064-.207.07-.317.076l-1.782.075c.354.234.702.33 1.094.46.065.022.702.2.69.28-.048.297-.317.616-.555.788"></path></svg>';
                        }

                        if(axie == "Bug"){
                            return '<svg width="16" height="16" viewBox="0 0 16 16" style="fill: #ff5341"><path d="M13.264 3.198c-1.565.33-3.077.994-4.084 2.216A6.19 6.19 0 008 7.704a6.19 6.19 0 00-1.18-2.29C5.813 4.192 4.301 3.527 2.736 3.198c-.357-.075-.75-.181-1.116-.197-.72-.031-.612.948-.616 1.439-.006.666.103 1.328.249 1.979.171.764.311 1.571.851 2.193.356.41.846.692 1.321.972.083.048.169.1.213.183.05.095.033.208.016.313l-.383 2.245c-.029.168-.048.366.081.484a.53.53 0 00.239.105c1.534.367 3.211-.478 4.01-1.774.176-.286.305-.585.399-.893.094.308.223.607.399.893.799 1.296 2.476 2.141 4.01 1.774a.529.529 0 00.238-.105c.13-.118.11-.316.082-.484l-.383-2.245c-.018-.105-.034-.218.016-.313.044-.082.13-.135.212-.183.476-.28.966-.561 1.322-.972.54-.622.68-1.43.851-2.193.146-.65.255-1.313.25-1.98-.005-.49.104-1.47-.617-1.438-.366.016-.759.122-1.116.197z"></path></svg>';
                        }

                        if(axie == "Dawn"){
                            return '<svg width="16" height="16" viewBox="0 0 16 16" style="fill: #129092;"><path d="M8.05985333,1.06695111 L8.10010932,1.07857965 C8.50590418,1.27947039 9.25287054,4.16489806 10.1120533,6.10864 C12.0905422,6.98668444 14.9276978,7.69868444 15.0453867,8.06277333 L15.0453867,8.06277333 C15.0510756,8.06668444 15.0487644,8.07059556 15.0482311,8.07450667 C15.0487644,8.07824 15.0510756,8.08232889 15.0510756,8.08606222 L15.0510756,8.08606222 C14.9276978,8.45032889 12.0905422,9.16215111 10.1120533,10.0401956 C9.22423111,12.0487289 8.45623111,15.0627733 8.06067556,15.08144 L8.06067556,15.08144 L8.06067556,15.0823289 L8.05765333,15.0821289 C7.66156444,15.0627733 6.89356444,12.0487289 6.00574222,10.0401956 C4.02707556,9.16215111 1.18992,8.45032889 1.07223111,8.08606222 L1.07223111,8.08606222 L1.06672,8.08606222 L1.06956444,8.07450667 C1.06885333,8.07059556 1.06672,8.06668444 1.06672,8.06277333 L1.06672,8.06277333 C1.18992,7.69868444 4.02707556,6.98668444 6.00574222,6.10864 C6.89356444,4.10010667 7.66156444,1.08606222 8.05694222,1.06757333 L8.05694222,1.06757333 L8.05985333,1.06695111 Z M13.0631289,11.3783289 C13.4341511,12.3591289 13.8265067,13.49904 13.6986844,13.62704 C13.5706844,13.7548622 12.4305956,13.3625067 11.4497956,12.9916622 C11.0789511,12.0106844 10.6864178,10.8705956 10.81424,10.7427733 C10.9420622,10.6149511 12.0821511,11.0073067 13.0631289,11.3783289 Z M5.39406222,10.6577067 C5.52170667,10.7855289 5.12935111,11.9256178 4.75832889,12.9064178 C3.77752889,13.27744 2.63761778,13.6697956 2.50961778,13.5419733 C2.38161778,13.4139733 2.77415111,12.2740622 3.14517333,11.2932622 C4.12597333,10.92224 5.26606222,10.5298844 5.39406222,10.6577067 Z M9.58896284,6.44819255 C9.41358578,6.43904672 8.64083556,6.88987852 8.03952,7.12250667 C7.40414222,6.87770667 6.61445333,6.40499556 6.50743111,6.45992889 L6.50743111,6.45992889 L6.50618667,6.45868444 C6.50316444,6.46241778 6.50174222,6.46295111 6.50085333,6.46384 L6.50085333,6.46384 C6.44752,6.57210667 6.92023111,7.36161778 7.16485333,7.99699556 C6.91560889,8.64126222 6.41605333,9.48268444 6.49996444,9.57477333 L6.49996444,9.57477333 L6.52880179,9.58473194 C6.7040557,9.59387852 7.47697185,9.14305778 8.07845333,8.91059556 C8.71383111,9.15521778 9.50316444,9.62810667 9.61036444,9.57317333 L9.61036444,9.57317333 C9.61232,9.57352889 9.61285333,9.57210667 9.61356444,9.57121778 L9.61356444,9.57121778 L9.61676444,9.56926222 C9.67045333,9.46081778 9.19756444,8.67130667 8.95276444,8.03592889 C9.20218667,7.39166222 9.70174222,6.55041778 9.61783111,6.45832889 L9.61783111,6.45832889 L9.61765333,6.45779556 L9.61729778,6.45726222 Z M13.6986844,2.69214222 C13.8265067,2.81996444 13.4341511,3.96005333 13.0631289,4.94067556 C12.0821511,5.31169778 10.9420622,5.70405333 10.81424,5.57623111 C10.6864178,5.44840889 11.0789511,4.30832 11.4497956,3.32752 C12.4305956,2.95649778 13.5706844,2.56414222 13.6986844,2.69214222 Z M4.75832889,3.24245333 C5.12935111,4.22325333 5.52170667,5.36334222 5.39406222,5.49116444 C5.26606222,5.61898667 4.12597333,5.22663111 3.14517333,4.85560889 C2.77415111,3.87498667 2.38161778,2.73489778 2.50961778,2.60689778 C2.63761778,2.47907556 3.77752889,2.87143111 4.75832889,3.24245333 Z"></path></svg>'
                        }

                        if(axie == "Reptile"){
                            return '<svg width="16" height="16" viewBox="0 0 16 16" style="fill: #dc8be4;"><path d="M13.927 8.056a1.32 1.32 0 00-1.309 1.152c-.135.191-.351.433-.583.476-.189.035-.457-.108-.607-.215a1.779 1.779 0 01-.696-1.186c-.105-.831-.308-2.175.324-2.857.026.001.053.004.08.004a1.47 1.47 0 10-1.263-.72c.058.758-.354 1.554-.954 2a2.022 2.022 0 01-.43.254c-.248.101-.595.1-.84 0a2.018 2.018 0 01-.43-.254c-.6-.446-1.012-1.242-.954-2a1.47 1.47 0 10-1.262.72c.026 0 .052-.003.079-.004.631.682.428 2.026.323 2.857-.057.455-.32.92-.696 1.186-.149.107-.418.25-.607.215-.232-.043-.447-.285-.582-.476a1.32 1.32 0 10-.619 1.294.655.655 0 01.331-.023c.62.122 1.095.604 1.481 1.071.413.499.86.949 1.43 1.262.327.18.672.336 1.029.448.195.06.443.088.647.114.464.06 1.002-.05 1.435-.217.255-.097.502-.214.741-.345.569-.313 1.017-.763 1.43-1.262.386-.467.861-.949 1.48-1.07a.654.654 0 01.331.022 1.321 1.321 0 10.69-2.446"></path></svg>';
                        }

                        if(axie == "Mech"){
                            return '<svg width="16" height="16" viewBox="0 0 16 16" style="fill: #c6bdd4;"><path d="M9.23004444,9.8361067 C9.52195556,9.87521781 9.84391111,9.86935115 10.1345778,9.83539559 C10.0403556,9.98632892 9.84337778,10.0949511 9.69511111,10.1827734 C9.31342222,10.4090845 8.85333333,10.4679289 8.41653333,10.4544178 C8.07502222,10.4437511 7.73671111,10.3552178 7.43111111,10.2032178 C7.12924444,10.0533511 6.8624,9.88588448 6.65102222,9.61815115 C6.64888889,9.61548448 6.64746667,9.61192892 6.64622222,9.60855115 C6.632,9.59504003 6.61688889,9.58312892 6.60284444,9.56926226 C5.79484444,8.76126226 5.79484444,7.45139559 6.60284444,6.64339559 C6.69173333,6.55468448 6.78737778,6.47735115 6.88675556,6.40819559 C6.59608889,6.36979559 6.27591111,6.37548448 5.98648889,6.40944003 C6.08071111,6.2585067 6.27768889,6.1497067 6.42595556,6.06206226 C6.80782222,5.83557337 7.26791111,5.7769067 7.70453333,5.79024003 C8.04604444,5.8009067 8.38435556,5.88961781 8.68995556,6.04144003 C8.92711111,6.1593067 9.14222222,6.28872892 9.32622222,6.4681067 C9.39626667,6.52197337 9.46435556,6.57939559 9.52871111,6.64339559 C10.3367111,7.45139559 10.3367111,8.76126226 9.52871111,9.56926226 C9.42791111,9.66988448 9.31964444,9.75806226 9.20533333,9.83344003 C9.21333333,9.83432892 9.22204444,9.83521781 9.23004444,9.8361067 M11.1130667,2.74437337 C10.5210667,2.39806226 5.6112,2.40944003 5.008,2.74437337 C4.4048,3.07948448 1.95555556,7.00215115 1.95555556,8.03166226 C1.95555556,9.06117337 4.41617778,12.8983289 5.008,13.3185956 C5.6,13.7388623 10.5098667,13.7280178 11.1130667,13.3185956 C11.7160889,12.9091734 14.1655111,8.86881781 14.1655111,8.03166226 C14.1655111,7.1945067 11.7050667,3.09104003 11.1130667,2.74437337"></path></svg>';
                        }

                        if(axie == "Dusk") {
                            return '<svg width="16" height="16" viewBox="0 0 16 16" style="fill: #beceff;"><path d="M7.63001266,1.62274177 C8.88782226,1.51583444 10.1636395,1.78756482 11.2673834,2.40169006 C11.8081596,2.7023783 12.3047352,3.08210876 12.7376473,3.52467232 C12.9490102,3.74103673 13.1452758,3.97233538 13.3242613,4.21638278 C13.4370367,4.36991408 13.5407173,4.54329701 13.6156584,4.71831906 C13.6345756,4.76239329 13.6573126,4.81174914 13.6616781,4.86019437 C13.6693177,4.94178634 13.6385773,4.93595834 13.5714577,4.89625511 C13.3972015,4.79371878 13.2387703,4.66732408 13.0688796,4.55823125 C12.8409641,4.41162069 12.5972237,4.28923274 12.3431153,4.19434566 C11.8178001,3.99783287 11.2510128,3.92152253 10.6924107,3.96905713 C8.59278724,4.14735743 7.03521333,5.99592539 7.21310744,8.09819336 C7.39118345,10.2004613 9.23760792,11.759997 11.3370495,11.5816967 C11.8849197,11.5352549 12.4202391,11.3704318 12.8982613,11.0985193 C13.1296328,10.9664787 13.3449974,10.8100334 13.5450828,10.6342829 C13.6867797,10.5097094 13.8115602,10.3864109 13.9381597,10.2479959 C13.9567131,10.2275979 14.0356559,10.1600296 14.0416584,10.2235912 C14.0487524,10.3000837 14.0414765,10.3714766 14.0291076,10.4474227 C13.9960026,10.6508563 13.9345218,10.8337097 13.8461204,11.020752 C13.7593561,11.2048803 13.6558574,11.3806308 13.5419906,11.5487321 C13.3066174,11.8960443 13.0272254,12.2125775 12.7263697,12.5036131 C12.4991819,12.7234379 12.2585337,12.929239 12.0062442,13.1193774 C11.0474713,13.8413206 9.90280093,14.2685857 8.71011004,14.3767679 C5.19260867,14.6752707 2.09947025,12.0625065 1.80097921,8.54057479 C1.50285196,5.01864306 4.11251129,1.92142664 7.63001266,1.62274177 Z M11.5244204,5.52724511 C11.6468362,5.52724511 12.0266347,6.32950539 12.3043896,6.95546875 C12.9326578,7.23320925 13.7011676,7.59581997 13.7011676,7.70673405 C13.7011676,7.818923 12.9159234,8.18827234 12.2838354,8.4671056 C12.005171,9.09980758 11.6362863,9.88622299 11.5244204,9.88622299 C11.4136458,9.88622299 11.0516731,9.11692732 10.7739182,8.48768571 C10.1487423,8.20976308 9.34749118,7.82948625 9.34749118,7.70673405 C9.34749118,7.58543885 10.1312803,7.21208276 10.753364,6.93488863 C11.0302094,6.3120214 11.4030958,5.52724511 11.5244204,5.52724511 Z"></path></svg>'
                        }
                    }
        },  
        methods:{
          clearFilter : function (){
                this.Clear = true;
                this.priceSearch = "Latest"
                this.auctionSearch = "Sale"
                this.mystic = 0
                this.purity = 0
                this.breed_count[0,7]
                this.selected = []
                this.selected_stage = []
                 this.Clear = false;
          },
          async currency_price(){
            const send = await this.$axios.get('/cryptoprice');
            const respo = await send.data;
            this.price = respo;
          },
          classaxie : function (axie){

                if(axie == "Beast"){
                    return "#ba8b1e"
                }

                if(axie == "Plant"){
                    return "#6cc000"
                }

                if(axie == "Aquatic"){
                    return "#00b8ce"
                }

                if(axie == "Bird"){
                    return "#503d52"
                }

                if(axie == "Bug"){
                    return "#ff5341"
                }

                if(axie == "Dawn"){
                    return "#129092";
                }

                if(axie == "Reptile"){
                    return "#dc8be4"
                }

                if(axie == "Mech"){
                    return "#c6bdd4"
                }

                if(axie == "Dusk") {
                    
                    return "#beceff"
                }
          },
        load_axies: _.debounce(async function(){
            
            if(this.Clear == false){
            this.axie = []
            const response = await this.$axios({
                method: 'post',

                url:'https://axieinfinity.com/graphql-server-v2/graphql',
                data:{
                        "operationName":"GetAxieBriefList",
                        "variables":{
                            "from":this.currentPage,
                            "size":24,
                            "sort":this.priceSearch,
                            "auctionType":this.auctionSearch,
                            "owner":null,
                            "criteria":{
                                "region":null,
                                "parts":null,
                                "bodyShapes":null,
                                "classes":this.selected,
                                "stages":this.selected_stage,
                                "numMystic":[
                                    this.mystic
                                ],
                                "pureness":null,
                                "title":null,
                                "breedable":null,
                                "breedCount":null,
                                "hp":[
                                    
                                ],
                                "skill":[
                                    
                                ],
                                "speed":[
                                    
                                ],
                                "morale":[
                                    
                                ]
                            }
                        },
                        "query":"query GetAxieBriefList($auctionType: AuctionType, $criteria: AxieSearchCriteria, $from: Int, $sort: SortBy, $size: Int, $owner: String) {\n  axies(auctionType: $auctionType, criteria: $criteria, from: $from, sort: $sort, size: $size, owner: $owner) {\n    total\n    results {\n      ...AxieBrief\n      __typename\n    }\n    __typename\n  }\n}\n\nfragment AxieBrief on Axie {\n  id\n  name\n  stage\n  class\n  breedCount\n  image\n  title\n  battleInfo {\n    banned\n    __typename\n  }\n  auction {\n    currentPrice\n    currentPriceUSD\n    __typename\n  }\n  parts {\n    id\n    name\n    class\n    type\n    specialGenes\n    __typename\n  }\n  __typename\n}\n"
                    },
                    headers: {
                        'Accept'       : 'application/json',
                        'Content-Type' : 'application/json',
                    },

        
                }
            );
            const store = await response.data;
            
            this.axie = store.data
            }
            
          },1000)
            
        },

        watch:{
            selected : function (){
                this.load_axies()
            },
            selected_stage : function (v){
                this.load_axies();
            },
            breed_count : function(){
                this.load_axies();
            },
            priceSearch : function(){
                this.load_axies();
            },

            auctionSearch : function(){
                this.load_axies();
            },
            mystic : function(v){
                this.load_axies();
            },
            currentPage : function(){
                this.load_axies();
            }
        },
        
        mounted(){
          this.load_axies();
        //   this.currency_price();
        }
        
    }
</script>

<style lang="scss" scoped>
.glasseffect{
    backdrop-filter: blur(16px) saturate(180%);
    -webkit-backdrop-filter: blur(16px) saturate(180%);
    background-color: rgba(17, 25, 40, 0.75);
    border-radius: 12px;
    border: 1px solid rgba(255, 255, 255, 0.125);
}


</style>