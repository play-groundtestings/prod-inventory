<script>

import { supabase } from '../supabase'
import { uid } from 'uid'


export default {
  name: "cardComponent",
  data() {
    return {
      tableRows: [],
      itemName: "",
      skuNo: "",
      date: "",
      joNo: "",
      itemType: "",
      itemColor: "",
      itemMaterial: "",
      text: "",
      sold: "Sold",
      buyDate: "",
      buyName: "",
      buyPrice: "",
      preparedBy: "",
      encodedBy: "",
      actual: "",
      replacement: "",
      retail: "",
      tag: "",
      quantity: "",
      goldHrs: "",
      goldName: "",
      goldMu: "",
      goldSp: "",
      goldRate: "",
      goldCost: "",
      stoneName: "",
      stoneMu: "",
      stoneSp: "",
      stoneHrs: "",
      stoneRate: "",
      stoneCost: "",
      polishHrs: "",
      polishRate: "",
      polishCost: "",
      polishMu: "",
      polishSp: "",
      polishName: "",
      laborCost: 0,
      totalMu: 1,
      craftcour: [],
      craftcurrt: [],
      craftrt: [],
      craftwt: [],
      craftsupinvdac: [],
      craftdescription: [],
      craftmatl: [],
      craftpcost: [],
      craftrepcost: [],
      craftsp: [],
      craftnoofpcs: [],
      craftunitcost: [],
      craftmu: [],
      uploadImageFile: null,
      imagePath: "",
      reserved: false,
      remade: false,
      borrowed: false,
      consigned: false
    }
  },
  computed: {
    totalActual() {
      return (parseFloat(this.tester) * 2).toFixed(3);
    },
    computeLaborCost() {

      var computeGold = 0;
      var computeStone = 0;
      var computePolish = 0;

      if (parseFloat(this.goldCost) > 0) {
        computeGold = parseFloat(this.goldCost)
      }
      if (parseFloat(this.stoneCost) > 0) {
        computeStone = parseFloat(this.stoneCost)
      }
      if (parseFloat(this.polishCost) > 0) {
        computePolish = parseFloat(this.polishCost)
      }

      this.laborCost = computeGold + computeStone + computePolish
      return this.laborCost.toFixed(2);
    },
    computeGoldSmith() {
      if (this.goldRate) {
        this.goldCost = (this.goldHrs * this.goldRate).toFixed(2);
      } else {
        this.goldCost = null
      }
    },
    computeStoneSmith() {
      if (this.stoneRate) {
        this.stoneCost = (this.stoneHrs * this.stoneRate).toFixed(2);
      } else {
        this.stoneRate = null
      }
    },
    computePolishSmith() {
      if (this.polishRate) {
        this.polishCost = (this.polishHrs * this.polishRate).toFixed(2);
      } else {
        this.polishRate = null
      }
    },
    computeFinalLaborSP() {
      if (this.totalMu) {
        return parseFloat(this.laborCost * this.totalMu).toFixed(2);
      } else {
        return "";
      }
    }
  },
  methods: {
    selectFile(event){
            this.uploadImageFile = event.target.files[0]
            
        },
    followUser() {
      this.followers++
    },
    addRow() {
      this.tableRows.push(
        {
          noOfPcs: "",
          matl: "",
          description: "",
          supInvDaCo: "",
          wt: "",
          unitCost: "",
          rt: "",
          totalP: "",
          curRt: "",
          totalRep: "",
          mu: "",
          cour: "",
          sp: "",
          remBut: ""
        });
    },
    computeCraftCost(index) {
      var fixedDecimal = 0;
      if (!this.craftrt[index]) {
        fixedDecimal = this.craftwt[index] * this.craftunitcost[index]
        this.craftpcost[index] = parseFloat(fixedDecimal).toFixed(2)
        this.craftrepcost[index] = parseFloat(fixedDecimal).toFixed(2)
      }
      if (this.craftrt[index]) {
        fixedDecimal = this.craftwt[index] * this.craftunitcost[index] * this.craftrt[index]
        this.craftpcost[index] = parseFloat(fixedDecimal).toFixed(2)
        this.craftrepcost[index] = parseFloat(fixedDecimal).toFixed(2)
      }
      if (this.craftcurrt[index]) {
        fixedDecimal = this.craftwt[index] * this.craftunitcost[index] * this.craftcurrt[index]
        this.craftrepcost[index] = parseFloat(fixedDecimal).toFixed(2)
      }
      if (!this.craftmu[index]) {
        fixedDecimal = this.craftrepcost[index]
        this.craftsp[index] = parseFloat(fixedDecimal).toFixed(2)
      }
      if (this.craftmu[index]) {
        fixedDecimal = this.craftrepcost[index] * this.craftmu[index]
        this.craftsp[index] = parseFloat(fixedDecimal).toFixed(2)
      }
    },
    removeRow(index) {
      this.craftcour[index] = "";
      this.craftcurrt[index] = "";
      this.craftrt[index] = "";
      this.craftwt[index] = "";
      this.craftsupinvdac[index] = "";
      this.craftdescription[index] = "";
      this.craftmatl[index] = "";
      this.craftpcost[index] = "";
      this.craftrepcost[index] = "";
      this.craftsp[index] = "";
      this.craftnoofpcs[index] = "";
      this.craftunitcost[index] = "";
      this.craftmu[index] = "";
      this.tableRows.splice(index, 1);
    },
    computeTotals(value) {
      var fixedDecimal = 0;
      fixedDecimal = value * this.quantity
      return parseFloat(fixedDecimal).toFixed(2)
    },
    computeCraftTot(value) {
      if (value.length > 0) {
        var arrayOfNumbers = value.map(Number);
        var sum = 0;
        for (const val of arrayOfNumbers) {
          sum += val;
        }
        return parseFloat(sum).toFixed(2);
      }
      else {
        return 0;
      }
    },
    async createCard() {
      var distinguisher = uid()

      if(this.uploadImageFile){
      const imageFile = this.uploadImageFile
      const fileExt = imageFile.name.split('.').pop()
      const generateName = uid()
      const fileName = `${generateName}.${fileExt}`
      this.imagePath = `https://lsckvveawgzilvwkhzbd.supabase.co/storage/v1/object/public/images/${fileName}`

      const { data, error } = await supabase.storage
      .from('images')
      .upload(fileName, imageFile)
      }

      const { inventorydata, inventoryerror } = await supabase
        .from('inventory')
        .insert([
        { id: distinguisher,
          inventorylink: distinguisher,
          itemName: this.itemName ? this.itemName : null,
          imgSrc: this.imagePath ? this.imagePath: null,
          skuNo: this.skuNo ? this.skuNo: null,
          date: this.date ? this.date: null,
          joNo: this.joNo ? this.joNo: null,
          itemType: this.itemType ? this.itemType: null,
          itemColor: this.itemColor ? this.itemColor: null,
          itemMaterial: this.itemMaterial ? this.itemMaterial: null,
          quantity: this.quantity ? this.quantity: 0,
          actual: this.actual ? this.actual: 0,
          totalActual: this.computeTotals(this.actual),
          replacement: this.replacement ? this.replacement: 0,
          totalReplacement: this.computeTotals(this.replacement),
          retail: this.retail ? this.retail: 0,
          totalRetail: this.computeTotals(this.retail),
          tag: this.tag ? this.tag: 0,
          totalTag: this.computeTotals(this.tag),
          totalCraftP: this.computeCraftTot(this.craftpcost),
          totalCraftRep: this.computeCraftTot(this.craftrepcost),
          totalCraftSp: this.computeCraftTot(this.craftsp),
          totalLaborActual: this.computeLaborCost,
          totalLaborMu: this.totalMu,
          totalLaborSp: this.computeFinalLaborSP ,
          preparedBy: this.preparedBy ? this.preparedBy : null,
          encodedBy: this.encodedBy ? this.encodedBy : null,
          availability: this.sold ? this.sold : null,
          buyName: this.buyName ? this.buyName : null,
          buyDate: this.buyDate ? this.buyDate : null,
          buyPrice: this.buyPrice ? this.buyPrice : 0,
          reserved: this.reserved,
          remade: this.remade,
          borrowed: this.borrowed,
          consigned: this.consigned },
          ])

          

          for(var i=0; i<this.tableRows.length; i++){
          const { craftingdata, craftingerror } = await supabase.from('crafting').insert([
            { id: uid(),
            inventorylink: distinguisher,
            noOfPcs: this.craftnoofpcs[i] ? this.craftnoofpcs[i]: null,
            material: this.craftmatl[i] ? this.craftmatl[i]: null,
            description: this.craftdescription[i] ? this.craftdescription[i]: null,
            supInvDaCo: this.craftsupinvdac[i] ? this.craftsupinvdac[i]: null,
            wt: this.craftwt[i] ? this.craftwt[i]: null,
            unitcost: this.craftunitcost[i] ? this.craftunitcost[i]: null,
            rt: this.craftrt[i] ? this.craftrt[i]: null,
            pCost: this.craftpcost[i] ? this.craftpcost[i]: null,
            curRt: this.craftcurrt[i] ? this.craftcurrt[i] : null,
            repCost: this.craftrepcost[i] ? this.craftrepcost[i]: null,
            mu: this.craftmu[i] ? this.craftmu[i]: null,
            cour: this.craftcour[i] ? this.craftcour[i]: null,
            sp: this.craftsp[i] ? this.craftsp[i]: null,
            },
          ])
        }

        const { golddata, golderror } = await supabase.from('labor').insert([
        { id: uid(),
        inventorylink: distinguisher,
        name: this.goldName ? this.goldName: null,
        hrs: this.goldHrs ? this.goldHrs: null,
        actualRate: this.goldRate ? this.goldRate: null,
        actualCost: this.goldCost ? this.goldCost: null,
        mu: this.goldMu ? this.goldMu : null,
        sp: this.goldSp ? this.goldSp : null,
        occupation: "goldsmith", },
        ])

        const { stonedata, stoneerror } = await supabase.from('labor').insert([
        { id: uid(),
        inventorylink: distinguisher,
        name: this.stoneName ? this.stoneName: null,
        hrs: this.stoneHrs ? this.stoneHrs: null,
        actualRate: this.stoneRate ? this.stoneRate: null,
        actualCost: this.stoneCost ? this.stoneCost: null,
        mu: this.stoneMu ? this.stoneMu : null,
        sp: this.stoneSp ? this.stoneSp : null,
        occupation: "stonesetter", },
       ])

       const { polishdata, polisherror } = await supabase.from('labor').insert([
        { id: uid(),
        inventorylink: distinguisher,
        name: this.polishName ? this.polishName: null,
        hrs: this.polishHrs ?  this.polishHrs: null,
        actualRate: this.polishRate ? this.polishRate: null,
        actualCost: this.polishCost ? this.polishCost: null,
        mu: this.polishMu ? this.polishMu : null,
        sp: this.polishSp ? this.polishSp : null,
        occupation: "polisher", },
        ]) 



      alert("Inventory Card Successfully Added.")
      this.$router.push('/')


    }
  },
  components: {

  },
  mounted() {

  }
}

</script>


<template>
  <form id="masterForm" @submit.prevent="createCard" autocomplete="off">
    <div class="row">
      <div class="col">
        <main>

          <div class="row padTop">
            <div class="col-auto">
              <label for="itemName" class="col-form-label">ITEM</label>
            </div>
            <div class="col-4">
              <input type="text" id="itemName" v-model="itemName" name="itemName" class="form-control">
            </div>
            <div class="col-auto">
              <input type="file" @change="selectFile">
            </div>
          </div>

          <div class="row padTop" id="firstRegion">
            <div class="col-auto">
              <label for="skuNo" class="col-form-label">SKU NO.</label>
            </div>
            <div class="col ">
              <input type="text" id="skuNo" v-model="skuNo" name="skuNo" class="form-control">
            </div>
            <div class="col-auto">
              <label for="date" class="col-form-label">DATE</label>
            </div>
            <div class="col">
              <input type="date" id="date" v-model="date" name="date" class="form-control">
            </div>
            <div class="col-auto">
              <label for="joNo" class="col-form-label">JO #</label>
            </div>
            <div class="col">
              <input type="text" id="joNo" v-model="joNo" name="joNo" class="form-control">
            </div>
            <div class="col-auto">
              <label for="itemType" class="col-form-label">TYPE</label>
            </div>
            <div class="col">
              <input type="text" id="itemType" v-model="itemType" name="itemType" class="form-control">
            </div>
            <div class="col-auto">
              <label for="itemColor" class="col-form-label">COLOR</label>
            </div>
            <div class="col">
              <input type="text" id="itemColor" v-model="itemColor" name="itemColor" class="form-control">
            </div>
            <div class="col-auto">
              <label for="itemMaterial" class="col-form-label">MATERIAL</label>
            </div>
            <div class="col">
              <input type="text" id="itemMaterial" v-model="itemMaterial" name="itemMaterial" class="form-control">
            </div>
            <div class="col-auto">
              <label for="quantity" class="col-form-label">QUANTITY</label>
            </div>
            <div class="col">
              <input type="text" id="quantity" v-model.number="quantity" name="quantity" class="form-control">
            </div>
          </div>

          <div class="row padTop" id="secondRegion">
            <div class="col-auto">
              <label for="actual" class="col-form-label">ACTUAL</label>
            </div>
            <div class="col">
              <input type="text" v-model.number="actual" id="actual" name="actual" class="form-control">
            </div>
            <div class="col-auto">
              <label for="replacement" class="col-form-label">REPLACEMENT</label>
            </div>
            <div class="col">
              <input type="replacement" v-model.number="replacement" id="replacement" name="replacement"
                class="form-control">
            </div>
            <div class="col-auto">
              <label for="retail" class="col-form-label">RETAIL</label>
            </div>
            <div class="col">
              <input type="text" id="retail" v-model.number="retail" name="retail" class="form-control">
            </div>
            <div class="col-auto">
              <label for="tag" class="col-form-label">TAG</label>
            </div>
            <div class="col">
              <input type="text" id="tag" v-model.number="tag" name="tag" class="form-control">
            </div>
          </div>

          <div class="row padTop" id="thirdRegion">
            <div class="col-auto">
              <label for="totalActual" class="col-form-label">TOTAL ACTUAL</label>
            </div>
            <div class="col">
              <input type="text" :value=computeTotals(actual) id="totalActual" name="totalActual" class="form-control">
            </div>
            <div class="col-auto">
              <label for="totalReplacement" class="col-form-label">TOTAL REPLACEMENT</label>
            </div>
            <div class="col">
              <input type="totalReplacement" :value=computeTotals(replacement) id="totalReplacement"
                name="totalReplacement" class="form-control">
            </div>
            <div class="col-auto">
              <label for="totalRetail" class="col-form-label">TOTAL RETAIL</label>
            </div>
            <div class="col">
              <input type="text" id="totalRetail" :value=computeTotals(retail) name="totalRetail" class="form-control">
            </div>
            <div class="col-auto">
              <label for="totalTag" class="col-form-label">TOTAL TAG</label>
            </div>
            <div class="col">
              <input type="text" id="totalTag" :value=computeTotals(tag) name="totalTag" class="form-control">
            </div>
          </div>

          <div class="row">
            <div class="col" style="margin:12px">
              <div class="row padTop" id="autoTable">
                <table class="col table-bordered" id="maintab">
                  <thead id="craftingMatStyle">
                    <tr>
                      <th class="text-center" colspan="13">CRAFTING MATERIALS</th>
                    </tr>
                    <tr>
                      <th scope="col">NO. OF PCS</th>
                      <th scope="col">MAT'L</th>
                      <th scope="col">DESCRIPTION</th>
                      <th scope="col">SUPPLIER/INV.#/DATE/CODE</th>
                      <th scope="col">WT.</th>
                      <th scope="col">UNIT COST</th>
                      <th scope="col">$ RT</th>
                      <th scope="col">TOTAL P COST (per item)</th>
                      <th scope="col">CUR. $ RT</th>
                      <th scope="col">TOTAL REP. COST (per item)</th>
                      <th scope="col">M.U.</th>
                      <th scope="col">COUR</th>
                      <th scope="col">S.P.</th>
                    </tr>
                  </thead>

                  <tbody>
                    <tr v-for="(tableRows, index) in tableRows" :key="index">
                      <td> <input type="text" class="form-control" :id="`noOfPcs-${index}`"
                          v-model.number="craftnoofpcs[index]" :name="`noOfPcs-${index}`"></td>
                      <td> <input type="text" class="form-control" v-model="craftmatl[index]" :id="`matl-${index}`"
                          :name="`matl-${index}`"></td>
                      <td> <input type="text" class="form-control" v-model="craftdescription[index]"
                          :id="`description-${index}`" :name="`description-${index}`"></td>
                      <td> <input type="text" class="form-control" v-model="craftsupinvdac[index]"
                          :id="`supInvDaCo-${index}`" :name="`supInvDaCo-${index}`"></td>
                      <td> <input type="text" class="form-control" v-model="craftwt[index]" :id="`wt-${index}`"
                          :name="`wt-${index}`"></td>
                      <td> <input type="text" class="form-control" @input="computeCraftCost(index)" :id="`unitCost-${index}`"
                           v-model.number="craftunitcost[index]"
                          :name="`unitCost-${index}`"></td>
                      <td> <input type="text" class="form-control" @input="computeCraftCost(index)" v-model="craftrt[index]" :id="`rt-${index}`"
                          :name="`rt-${index}`"></td>
                      <td> <input type="text" class="form-control" :ref="`totalP-${index}`" :id="`totalP-${index}`"
                          v-model="craftpcost[index]" :name="`totalP-${index}`"></td>
                      <td> <input type="text" class="form-control" v-model="craftcurrt[index]" @input="computeCraftCost(index)" :id="`curRt-${index}`"
                          :name="`curRt-${index}`"></td>
                      <td> <input type="text" class="form-control" :id="`totalRep-${index}`"
                          v-model="craftrepcost[index]" :name="`totalRep-${index}`"></td>
                      <td> <input type="text" class="form-control" :id="`mu-${index}`" @input="computeCraftCost(index)"
                          :name="`mu-${index}`" v-model.number="craftmu[index]"></td>
                      <td> <input type="text" class="form-control" v-model="craftcour[index]" :id="`cour-${index}`"
                          :name="`cour-${index}`"></td>
                      <td> <input type="text" class="form-control" :id="`sp-${index}`" v-model="craftsp[index]"
                          :name="`sp-${index}`"></td>
                    </tr>
                    <tr v-if="tableRows.length > 0" id="lastCraftRow">
                      <td></td>
                      <td></td>
                      <td></td>
                      <td></td>
                      <td></td>
                      <td></td>
                      <td></td>
                      <td id="totalPCraft"><strong>TOTAL P COST: {{computeCraftTot(craftpcost)}} </strong></td>
                      <td></td>
                      <td id="totalRepCraft"><strong>TOTAL REP COST: {{computeCraftTot(craftrepcost)}}</strong></td>
                      <td></td>
                      <td></td>
                      <td id="totalSpCraft"><strong>FINAL SP: {{computeCraftTot(craftsp)}}</strong></td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </div>

            <div class="col-1">
              <div class="row">
                <th scope="col"><a id="craftMatButton" @click="addRow">+</a></th>
              </div>
              <div class="row">
                <th scope="col"><a id="craftRemButton" @click="removeRow(this.tableRows.length-1)">-</a></th>
              </div>
            </div>
          </div>

          <div class="row padTop" id="lastRowPad">

            <div class="col" id="laborTable">
              <table class="table-bordered">
                <thead>
                  <tr>
                    <th class="text-center" colspan="7">LABOR</th>
                  </tr>
                  <tr>
                    <th scope="col"> </th>
                    <th scope="col">NAME</th>
                    <th scope="col">HRS</th>
                    <th scope="col">ACTUAL RATE</th>
                    <th scope="col">ACTUAL COST</th>
                    <th scope="col">M.U.</th>
                    <th scope="col">SP</th>
                  </tr>
                </thead>
                <tbody>
                  <tr>
                    <th scope="row">Goldsmith</th>
                    <td><input type="text" v-model="goldName" id="goldName" name="goldName" class="form-control"></td>
                    <td><input type="text" v-model.number="goldHrs" id="goldHrs" name="goldHrs" class="form-control">
                    </td>
                    <td><input type="text" v-model.number="goldRate" @input="computeGoldSmith" id="goldRate"
                        name="goldRate" class="form-control"></td>
                    <td><input type="text" v-model="goldCost" id="goldCost" name="goldCost" class="form-control"></td>
                    <td><input type="text" v-model.number="goldMu" id="goldMu" name="goldMu" class="form-control"></td>
                    <td><input type="text" v-model.number="goldSp" id="goldSp" name="goldSp" class="form-control"></td>
                  </tr>
                  <tr>
                    <th scope="row">Stone Setter</th>
                    <td><input type="text" v-model="stoneName" id="stoneName" name="stoneName" class="form-control">
                    </td>
                    <td><input type="text" v-model.number="stoneHrs" id="stoneHrs" name="stoneHrs" class="form-control">
                    </td>
                    <td><input type="text" v-model.number="stoneRate" id="stoneRate" @input="computeStoneSmith"
                        name="stoneRate" class="form-control"></td>
                    <td><input type="text" v-model="stoneCost" id="stoneCost" name="stoneCost" class="form-control">
                    </td>
                    <td><input type="text" v-model.number="stoneMu" id="stoneMu" name="stoneMu" class="form-control">
                    </td>
                    <td><input type="text" v-model.number="stoneSp" id="stoneSp" name="stoneSp" class="form-control">
                    </td>
                  </tr>
                  <tr>
                    <th scope="row">Polisher</th>
                    <td><input type="text" v-model="polishName" id="polishName" name="polishName" class="form-control">
                    </td>
                    <td><input type="text" v-model.number="polishHrs" id="polishHrs" name="polishHrs"
                        class="form-control"></td>
                    <td><input type="text" v-model.number="polishRate" @input="computePolishSmith" id="polishRate"
                        name="polishRate" class="form-control"></td>
                    <td><input type="text" v-model="polishCost" id="polishCost" name="polishCost" class="form-control">
                    </td>
                    <td><input type="text" v-model.number="polishMu" id="polishMu" name="polishMu" class="form-control">
                    </td>
                    <td><input type="text" v-model.number="polishSp" id="polishSp" name="polishSp" class="form-control">
                    </td>

                  </tr>
                  <tr id="lastLaborRow">
                    <th scope="row"></th>
                    <td></td>
                    <td></td>
                    <td></td>
                    <td id="totalLaborCost"><strong>TOTAL LABOR COST: {{computeLaborCost}} </strong></td>
                    <td id="totalLaborMu"><strong>MU: <input type="text" v-model.number="totalMu" id="totalMu"
                          name="totalMu" class="form-control"></strong></td>
                    <td id="totalLaborSp"><strong>FINAL SP: {{computeFinalLaborSP}} </strong></td>
                  </tr>
                </tbody>
              </table>

            </div>

            <div class="col" id="lastRegion">
              <div class="row">
                <div class="col-auto">
                  <label for="preparedBy" class="col-form-label">PREPARED BY</label>
                </div>
                <div class="col ">
                  <input type="text" id="preparedBy" v-model="preparedBy" name="preparedBy" class="form-control">
                </div>
                <div class="col-auto">
                  <label for="encodedBy" class="col-form-label">ENCODED BY</label>
                </div>
                <div class="col ">
                  <input type="text" id="encodedBy" v-model="encodedBy" name="encodedBy" class="form-control">
                </div>
              </div>

              <div class="row" id="optionsForm">                

                <div class="col-auto">
                  <input type="checkbox" name="reserved" id="reserved" v-model="reserved"/>
                </div>
                <div class="col-auto">
                  <label for="reserved">RESERVED</label>
                </div>

                <div class="col-auto">
                  <input type="checkbox" name="remade" id="remade" v-model="remade"/>
                </div>
                <div class="col-auto">
                  <label for="remade">REMADE</label>
                </div>

                <div class="col-auto">
                  <input type="checkbox" name="borrowed" id="borrowed" v-model="borrowed"/>
                </div>
                <div class="col-auto">
                  <label for="borrowed">BORROWED</label>
                </div>

                <div class="col-auto">
                  <input type="checkbox" name="consigned" id="consigned" v-model="consigned"/>
                </div>
                <div class="col-auto">
                  <label for="remade">CONSIGNED</label>
                </div>
              </div>


              <div class="row" id="radioForm">

                <label>AVAILABILITY: {{sold}} </label>


                <div class="col-auto">
                  <input type="radio" name="availability" id="isSold" v-model="sold" value="Sold" checked />
                </div>
                <div class="col-auto">
                  <label for="isSold">SOLD</label>
                </div>

                <div class="col-auto">
                  <input type="radio" name="availability" id="isStocked" v-model="sold" value="In-Stock" />
                </div>
                <div class="col-auto">
                  <label for="isStocked">IN STOCK</label>
                </div>
              </div>

              <div v-if="sold==='Sold'">
                <div class="row" id="lastFormRow">
                  <div class="col mb-3">
                    <label for="buyName" class="form-label">SOLD TO</label>
                    <input type="text" class="form-control" v-model="buyName" name="buyName" id="buyName">
                  </div>
                  <div class="col mb-3">
                    <label for="buyDate" class="form-label">DATE</label>
                    <input type="date" class="form-control" v-model="buyDate" id="buyDate" name="buyDate">
                  </div>
                  <div class="col mb-3">
                    <label for="buyPrice" class="form-label">PRICE</label>
                    <input type="text" class="form-control" v-model="buyPrice" id="buyPrice" name="buyPrice">
                  </div>
                </div>
              </div>

              <div class="row">
                <button type="submit" class="btn btn-light" style="border-color:black;">Submit</button>
              </div>

            </div>
          </div>
        </main>
      </div>
    </div>
  </form>
</template>

<style>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

.logo {
  margin: 0 2rem 0 0;
}


#inventoryNavBar a {
  color: black;
  text-decoration: none;
}

#inventoryNavBar>a {
  font-weight: bold;
}

.greetings .green {
  color: blueviolet;
}

#secondRegion,
#autoTable {
  margin-top: 10px;
  margin-bottom: 10px;
}

#lastCraftRow {
  border-color: white;
}

#lastLaborRow {
  border-color: white;
}

#lastRowPad {
  margin-top: 10px;
}

.table-bordered #lastCraftRow #totalSpCraft {
  border: 2px #505050 solid;
  font-size: small
}

.table-bordered #lastCraftRow #totalRepCraft {
  border: 2px #505050 solid;
  font-size: small
}

.table-bordered #lastCraftRow #totalPCraft {
  border: 2px #505050 solid;
  font-size: small
}

#laborTable #totalLaborCost {
  border: 2px #505050 solid;
  font-size: small
}

#laborTable #totalLaborMu {
  border: 2px #505050 solid;
  font-size: small
}

#laborTable #totalLaborSp {
  border: 2px #505050 solid;
  font-size: small
}

label {
  font-weight: bold;
  padding: 0px;
}

#craftMatButton {
  cursor: pointer;
  font-size: 200%;
  text-align: center;
}

#craftRemButton {
  cursor: pointer;
  font-size: 200%;
  padding-left: 5%;
  text-align: center;
}

.removeStyling {
  cursor: pointer;
  font-size: 160%;
  text-decoration: none;
  color: black;
  padding-left: 2.5px;
}

#lastRegion {
  padding-top: 1.5%;

}

#radioForm {
  padding-top: 2.5%;
  padding-bottom: 2.5%;
}

#optionsForm {
  margin-top: 2%;
}


#firstRegion {
  padding-top: 1%;
}

</style>
