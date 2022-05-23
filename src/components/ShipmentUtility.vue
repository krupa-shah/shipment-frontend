<template>
  <section class="rows">
    <b-field>
      <b-input v-model="updateDB" type="text" placeholder="updateDB" />
      <div class="buttons">
        <b-button type="is-primary is-light" @click="update()">Update</b-button>
        <b-button type="is-primary is-light" @click="print()"
          >Print Shipment</b-button
        >
      </div>
    </b-field>

    <!--print shipment -->
    <div v-if="printShipment">
      <div v-for="(shipments, index) in DB" :key="'ship' + index">
        Shipment # {{ index + 1 }} : Number: {{ shipments.ShipmentNumber }} ,
        Order Number: {{ shipments.OrderNumber }}, Shipped:
        {{ shipments.Shipped }}, First Name: {{ shipments.FirstName }}, Last
        Name: {{ shipments.LastName }}, Parent Shipment:
        {{ shipments.ParentShipment }}
      </div>
    </div>
    <!-- return properties of shipment -->
    <b-field>
      <b-input
        v-model="shipmentNumber"
        type="text"
        placeholder="enter shipment number"
      />
      <div class="buttons">
        <b-button
          type="is-primary is-light"
          @click="fetchProperties(shipmentNumber)"
          >GET</b-button
        >
        <b-button
          type="is-primary is-light"
          @click="fetchPropertiesByShipmentNumber(shipmentNumber)"
        >
          Fetch With Add-On Properties
        </b-button>
      </div>
      {{ returnShipment }}
    </b-field>
    <!-- return properties of order -->
    <b-field>
      <b-input
        v-model="orderNumber"
        type="text"
        placeholder="enter order number"
      />
      <b-button
        type="is-primary is-light"
        @click="fetchPropertiesByOrderNumber(orderNumber)"
        >GET</b-button
      >

      {{ returnOrder }}
    </b-field>
    <!-- sort order-->
    <div class="buttons">
      <b-button type="is-primary is-light" @click="sortByDays()">SORT</b-button>
      <b-button
        type="is-primary is-light"
        @click="toggleSortByDays('DaysAgoShipped')"
        >Toggle SORT</b-button
      >
      <b-button
        type="is-primary is-light"
        @click="sortByAdditionalProperty('FirstName')"
      >
        Toggle SORT with addon Parameter
      </b-button>
    </div>
    <b-table :data="returnSorted" :columns="columns"> </b-table>

    <!-- return parent shipment -->
    <b-field>
      <b-input
        v-model="parentShipmentNumber"
        type="text"
        placeholder="enter shipment number"
      />
      <b-button
        type="is-primary is-light"
        @click="fetchParentShipment(parentShipmentNumber)"
        >GET</b-button
      >

      {{ returnParent }}
    </b-field>
  </section>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      updateDB: "",
      DB: [],
      printShipment: false,
      shipmentNumber: "",
      parentShipmentNumber: "",
      DBwithAddOns: [],
      returnShipment: undefined,
      returnOrder: undefined,
      returnParent: undefined,
      orderNumber: "",
      asc: true,
      columns: [ // table headers
        {
          field: "id",
          label: "Shipment",
          centered: true,
        },
        {
          field: "ShipmentNumber",
          label: "Number",
          centered: true,
        },
        {
          field: "OrderNumber",
          label: "Order Number",
          centered: true,
        },
        {
          field: "Shipped",
          label: "Shipped",
          centered: true,
        },
        {
          field: "FirstName",
          label: "First Name",
          centered: true,
        },
        {
          field: "LastName",
          label: "Last Name",
          centered: true,
        },
        {
          field: "fullname",
          label: "Full Name",
          centered: true,
        },
        {
          field: "ParentShipment",
          label: "Parent Shipment",
          centered: true,
        },
        {
          field: "DaysAgoShipped",
          label: "Days Shipped Ago",
          centered: true,
        },
      ],
    };
  },
  computed: {
    returnSorted() {
      return this.DBwithAddOns;
    },
  },
  methods: {
    /**
     * Update the temp DB with user input for shipment data to work through requirements.
     */
    update() {
      const array = this.updateDB.split(",");
      const obj = {
        ShipmentNumber: array[0],
        OrderNumber: array[1] ? array[1] : "N/A",
        Shipped: array[2],
        FirstName: array[3],
        LastName: array[4],
        ParentShipment: array[5] ? array[5] : "N/A",
      };
      this.DB.push(obj);
      this.updateDB = "";
    },
    /**
     * Prints out shipment in standard output. Reqt 1
     */
    print() {
      this.printShipment = !this.printShipment;
    },
    /**
     * param: shipmentNumber
     * returns: All shipment properties for the associated number
     * Reqt 2
     */
    fetchProperties(number) {
      this.returnShipment = this.DB.find(
        (item) => item.ShipmentNumber === number
      );
    },
    /**
     * Store the DB with addon values : fullname and  daysagoshipped
     */
    updateAddOnDB() {
      this.DBwithAddOns = [...this.DB];
      var todaysDate = new Date();
      this.DBwithAddOns.forEach((item, index) => {
        item.id = ++index;
        item.fullname = item.FirstName + " " + item.LastName;
        let date2 = new Date(item.Shipped);
        item.DaysAgoShipped = parseInt(
          (todaysDate - date2) / (1000 * 60 * 60 * 24),
          10
        );
      });
    },
    /**
     * param: shipmentNumber
     * return: all shipment properties with additional two computed properties
     * Reqt 3
     */
    fetchPropertiesByShipmentNumber(number) {
      if (this.DBwithAddOns.length < this.DB.length) this.updateAddOnDB();
      this.returnShipment = "";
      this.returnShipment = this.DBwithAddOns.find(
        (item) => item.ShipmentNumber === number
      );
    },
    /**
     * param: OrderNumber
     * return: all order number properties with additional two computed properties
     * Reqt 4
     */
    fetchPropertiesByOrderNumber(order) {
      if (this.DBwithAddOns.length < this.DB.length) this.updateAddOnDB();
      this.returnOrder = "";
      this.returnOrder = this.DBwithAddOns.find(
        (item) => item.OrderNumber === order
      );
    },
    /**
     * Sort data by DaysAgoshipped property
     * Reqt 5
     */
    sortByDays() {
      if (this.DBwithAddOns.length < this.DB.length) this.updateAddOnDB();
      this.DBwithAddOns.sort((a, b) => {
        return a.DaysAgoShipped - b.DaysAgoShipped;
      });
    },
    /**
     * toggle the sorted data by property passed
     * Reqt 6
     */
    toggleSortByDays(arg) {
      if (this.DBwithAddOns.length < this.DB.length) this.updateAddOnDB();
      this.asc = !this.asc;
      this.DBwithAddOns.sort((a, b) => {
        return this.asc ? b[arg] - a[arg] : a[arg] - b[arg];
      });
    },
    /**
     * sort the data by daysagoshipped and an additional agrument passed in function
     * Reqt 7
     */
    sortByAdditionalProperty(arg) {
      if (this.DBwithAddOns.length < this.DB.length) this.updateAddOnDB();
      this.asc = !this.asc;
      this.DBwithAddOns.sort((a, b) => {
        return this.asc
          ? b["DaysAgoShipped"] - a["DaysAgoShipped"] || b[arg] - a[arg]
          : a["DaysAgoShipped"] - b["DaysAgoShipped"] || a[arg] - b[arg];
      });
    },
    /**
     * param: Shipment Number
     * Return the orginal shipment
     * Reqt 8
     */
    fetchParentShipment(number) {
      if (this.DBwithAddOns.length < this.DB.length) this.updateAddOnDB();
      while (number !== "N/A") {
        const shipment = this.DBwithAddOns.find(
          (item) => item.ShipmentNumber === number
        );

        if (shipment.ParentShipment === "N/A") {
          this.returnParent = shipment;
          break;
        } else number = shipment.ParentShipment;
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
