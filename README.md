//A változók létrehozássa.
var cart = {
  //tárgyak száma
  count: 0,
  //teljes összeg
  total: 0, 
  //tárgyak tömb
  items: [],
 
  //Kosár tartalmának megjelenítése egy táblázatban
  render: function () {
    //Tábla eleje
      s = `<table>`;
      for (var i = 0; i < this.items.length; i++) {
          s += `<tr>
              <td>${this.items[i].id}</td>
              <td>${this.items[i].name}</td>
              <td>${this.items[i].price}</td>
              <td><input type="number" onchange="cart.updateItem(${this.items[i].id},Number(this.value))" min=1 value=${this.items[i].count}></td>
              <td>${this.items[i].total}</td>
              <td><button onclick="cart.removeItem(${this.items[i].id})">Töröl</button></td>
          </tr>`;
      }
      //Tábla vége
      s += `</table>`;
      //Ha count = 0, akkor kosár üres :O
      if (this.count == 0) {
          s += `<div>A kosár üres<\div>`
          //Ha nem üres
      } else {
        //Kiírja összesen hány tárgy van a kosárban
          s += `<div>Összesen: ${this.total} Ft<\div>`
          s += `<div>Összesen ${this.count} db tárgy van a kosárban <\div>`
      }
 
      return s;
  },
  //Kosár újratöltése, gondolom mikor hozzáadunk valamit vagy kitörlünk belőle
  refresh: function () {
      document.getElementById("kosar").innerHTML = cart.render();
  },
  containsItem: function (id) {
      let van = 0;
      for (var i = 0; i < this.items.length; i++) {
          if (id == this.items[i].id) {
              van = 1;
              return true;
          }
      }
      if (van == 0) {
          return false;
      }
  },
  //Tárgy hozzáadása
  addItem: function kosar(item) {
      item.count = Number(document.getElementById(`db_${item.id}`).value);
      // Ellenőrzi, hogy van-e már ilyen tárgy a kosárban
      if (this.containsItem(item.id)) {
          console.log("Van ilyen id:" + item)
      } else {
          console.log("Add id:" + item)
          //Items tömbhöz hozzábaszunk egy objektumot ami tartalmazza a tárgyunk adatait
          this.items.push({
              id: item.id,
              name: item.name,
              price: item.price,
              count: item.count,
              total: item.price * item.count,
          });
          console.log(this.items);
          //összes és count növelése
          this.total += item.price * item.count;
          this.count += item.count;
          //kosár újratöltése
          this.refresh();
      }
 
  },
  //Tárgy törlése a kosárból
  removeItem: function (id) {
      for (var i = 0; i < this.items.length; i++) {
        //Ha az i megegyezik az items i-edik elemének id-jével akkor törli az items[i]-t valamint csökkenti a count és total változókat
          if (this.items[i].id == id) {
              this.total -= this.items[i].total;
              this.count -= this.items[i].count;
              this.items.splice(i, 1);
              //kosár frissítése és mentése
              this.refresh();
              this.savecart();
 
              break;
          }
      }
  },
  //Tárgy frissítése
  updateItem: function (id, db) {
      for (var i = 0; i < this.items.length; i++) {
 
          if (this.items[i].id == id) {
              this.count -= this.items[i].count;
              this.total -= this.items[i].total; // Régi érték levonása
              this.items[i].count = db;
              this.items[i].total = this.items[i].price * db;
              this.total += this.items[i].total; // Új érték hozzáadása
              this.count += db;
              //kosár frissítése és mentése
              this.refresh();
              this.savecart();
          }
      }
  },
 
  //Kosár mentése a localStorage-be
  savecart: function () {
    //localStorage-ben cart néven mentjük a kosár stringgé alakított objektumát
      localStorage.setItem("cart", JSON.stringify(this.items));
  },
  //Kosár lekérése a localStorage-ből
  getCrat: function () {
    //kikérjük localStorage-ből a cart nevű stringet és JSON objektummá alakítjuk és hozzárendeljük az items tömbhöz
      this.items = JSON.parse(localStorage.getItem("cart"));
  },
  //Kosár beolvasása 
  fetchData: function () {
      this.items = data;
  },
}
 
//Termékek objektum
var termekek = {
  //tárgyak tömb
  items: [],
  //mutatás
  show: function () {
    //Data hozzárendelése az items tömbhöz (beolvasás)
      this.items = data;
      //Tábla eleje
      s = "<table>";
      //végigmegy az összes terméken és hozzáadja őket a táblához
      for (var i = 0; i < this.items.length; i++) {
          let termek = `{ id: ${this.items[i].id}, name: '${this.items[i].name}', price: ${this.items[i].price}, count: 1, total: ${this.items[i].price * this.items[i].count}, }`;
          s += `<tr>
                  <td>${this.items[i].id}</td>
                  <td>${this.items[i].name}</td>
                  <td>${this.items[i].price}</td>
                  <td><input min=1 type="number" id="db_${this.items[i].id}" value=1 ><td>
                  <td><button onclick="cart.addItem(${termek})">Kosárba</button></td>
              </tr>`;
      }
      //Tábla vége
      s += `</table>`;
 
      //Visszaadja a legenerált tábla HTML kódját
      return s;
  },
}
 
//Betöltés event
document.addEventListener('DOMContentLoaded', function () {
  //Kiírja ezt a szart ha betöltött
  console.log('Betöltődött.');
  //Megkeresi a HTML fájlban a "termekek" id-vel ellátott elemet és beleírja a termékek táblát
  document.getElementById("termekek").innerHTML = termekek.show();
});
 
