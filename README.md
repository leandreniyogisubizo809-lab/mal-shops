# mal-shops
<!--
M.A.L Shops - Single-file demo store
Save this as `index.html` and open in your browser.
Simple, friendly, and commented so you (age 12!) can edit:
- Change the shop name (search for SHOP_NAME)
- Add or remove products in the `products` array in the <script> section
- Improve styles or replace images with your own

Features:
- Product grid with sample items
- Add to cart + simple cart UI
- Search and category filter
- Mobile-friendly
- No back-end required (pure front-end demo)

Have fun building it further — I included tips below the file.
-->

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>M.A.L Shops — Mini Store</title>
  <style>
    :root{
      --accent:#0b67ff;
      --muted:#6b7280;
      --card:#ffffff;
      --bg:#f7f7fb;
      --radius:12px;
      font-family: Inter, ui-sans-serif, system-ui, -apple-system, 'Segoe UI', Roboto, 'Helvetica Neue', Arial;
    }
    html,body{height:100%;margin:0;background:var(--bg);color:#0f1724}
    .container{max-width:1100px;margin:24px auto;padding:16px}
    header{display:flex;align-items:center;justify-content:space-between;gap:12px}
    .brand{display:flex;align-items:center;gap:12px}
    .logo{width:56px;height:56px;border-radius:12px;background:linear-gradient(135deg,#5eead4,#0ea5e9);display:flex;align-items:center;justify-content:center;color:#05263b;font-weight:700;font-size:18px}
    h1{margin:0;font-size:20px}
    p.lead{margin:0;color:var(--muted)}

    /* controls */
    .controls{display:flex;gap:8px;align-items:center}
    input.search{padding:10px 12px;border-radius:10px;border:1px solid #e6e9ef;background:white}
    select{padding:10px;border-radius:10px;border:1px solid #e6e9ef}
    button.btn{background:var(--accent);color:white;padding:10px 12px;border-radius:10px;border:0;cursor:pointer}

    /* layout */
    .main{display:grid;grid-template-columns:1fr 320px;gap:20px;margin-top:18px}
    @media (max-width:900px){.main{grid-template-columns:1fr} .cart-panel{order:-1}}

    /* products */
    .grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(220px,1fr));gap:16px}
    .card{background:var(--card);border-radius:var(--radius);padding:12px;box-shadow:0 6px 18px rgba(15,23,36,0.05);display:flex;flex-direction:column;gap:8px}
    .thumb{height:140px;border-radius:10px;overflow:hidden;background:#eef2ff;display:flex;align-items:center;justify-content:center}
    .thumb img{width:100%;height:100%;object-fit:cover}
    .title{font-weight:600}
    .price{font-weight:700}
    .meta{color:var(--muted);font-size:13px}

    /* cart */
    .cart-panel{background:linear-gradient(180deg,#ffffff,#fcfcff);padding:12px;border-radius:12px;height:fit-content;position:sticky;top:16px}
    .cart-list{display:flex;flex-direction:column;gap:10px}
    .cart-item{display:flex;gap:8px;align-items:center}
    .qty-btn{padding:6px 8px;border-radius:8px;border:1px solid #e6e9ef;background:white;cursor:pointer}
    .empty{color:var(--muted);text-align:center;padding:28px}

    footer{margin-top:28px;text-align:center;color:var(--muted);font-size:13px}

    /* small helpers */
    .pill{background:#f1f5f9;padding:6px 8px;border-radius:999px;font-weight:600}
    .row{display:flex;gap:8px;align-items:center}
  </style>
</head>
<body>
  <div class="container">
    <header>
      <div class="brand">
        <div class="logo">M.A.L</div>
        <div>
          <h1>M.A.L Shops</h1>
          <p class="lead">Friendly store — we sell all kinds of stuff!</p>
        </div>
      </div>

      <div class="controls">
        <input id="searchInput" class="search" placeholder="Search products..." />
        <select id="categoryFilter">
          <option value="all">All categories</option>
          <option value="clothing">Clothing</option>
          <option value="gadgets">Gadgets</option>
          <option value="toys">Toys</option>
          <option value="home">Home</option>
        </select>
        <button class="btn" id="clearBtn">Clear</button>
      </div>
    </header>

    <div class="main">
      <section>
        <div style="display:flex;justify-content:space-between;align-items:center">
          <h2 style="margin:12px 0 6px">Products</h2>
          <div class="pill" id="resultsCount">0 items</div>
        </div>

        <div id="productGrid" class="grid"></div>

        <div style="margin-top:18px;display:flex;gap:8px;justify-content:center">
          <small class="meta">This site is a demo. Replace images and products in the code to sell your own items.</small>
        </div>
      </section>

      <aside class="cart-panel">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <strong>Your cart</strong>
          <small id="cartCount" class="meta">0</small>
        </div>

        <div id="cartList" class="cart-list"></div>
        <div id="cartEmpty" class="empty">Your cart is empty</div>

        <div style="margin-top:12px;display:flex;flex-direction:column;gap:8px">
          <div style="display:flex;justify-content:space-between"><span>Subtotal</span><strong id="subtotal">$0.00</strong></div>
          <button class="btn" id="checkoutBtn">Checkout</button>
        </div>
      </aside>
    </div>

    <footer>
      <small>M.A.L Shops — Made with ❤️ by friends learning to code</small>
    </footer>
  </div>

  <script>
    /***** EDIT THIS SECTION TO ADD OR CHANGE PRODUCTS *****/
    const SHOP_NAME = 'M.A.L Shops' // change your shop name here

    // Products: id, title, price (number), category, image
    // You can replace image URLs with your own images (hosted online) or local files.
    const products = [
      {id:1, title:'Comfy T-shirt', price:14.99, category:'clothing', img:'https://picsum.photos/seed/tshirt/400/300'},
      {id:2, title:'Wireless Earbuds', price:29.99, category:'gadgets', img:'https://picsum.photos/seed/earbuds/400/300'},
      {id:3, title:'Super Fun Toy', price:9.5, category:'toys', img:'https://picsum.photos/seed/toy/400/300'},
      {id:4, title:'Decor Lamp', price:22.0, category:'home', img:'https://picsum.photos/seed/lamp/400/300'},
      {id:5, title:'Cool Cap', price:11.0, category:'clothing', img:'https://picsum.photos/seed/cap/400/300'},
      {id:6, title:'Phone Stand', price:7.75, category:'gadgets', img:'https://picsum.photos/seed/stand/400/300'},
    ]

    /***** App State *****/
    let cart = {} // { productId: qty }

    /***** Helpers *****/
    const $ = id => document.getElementById(id)
    const formatMoney = n => '$' + n.toFixed(2)

    /***** Render products *****/
    function renderProducts(list){
      const grid = $('productGrid')
      grid.innerHTML = ''
      list.forEach(p => {
        const card = document.createElement('div')
        card.className = 'card'
        card.innerHTML = `
          <div class="thumb"><img src="${p.img}" alt="${p.title}" /></div>
          <div style="display:flex;justify-content:space-between;align-items:start">
            <div>
              <div class="title">${p.title}</div>
              <div class="meta">${p.category}</div>
            </div>
            <div class="price">${formatMoney(p.price)}</div>
          </div>
          <div style="display:flex;gap:8px;align-items:center;justify-content:space-between;margin-top:6px">
            <button class="btn" onclick="addToCart(${p.id})">Add to cart</button>
            <button style="background:#eef2ff;border:0;padding:8px;border-radius:8px;cursor:pointer" onclick="viewDetails(${p.id})">Details</button>
          </div>
        `
        grid.appendChild(card)
      })
      $('resultsCount').innerText = list.length + ' items'
    }

    function viewDetails(id){
      const p = products.find(x=>x.id===id)
      alert(p.title + '\n\nPrice: ' + formatMoney(p.price) + '\nCategory: ' + p.category)
    }

    /***** Cart functions *****/
    function addToCart(id){
      cart[id] = (cart[id] || 0) + 1
      renderCart()
    }
    function changeQty(id, delta){
      cart[id] = (cart[id] || 0) + delta
      if(cart[id] <= 0) delete cart[id]
      renderCart()
    }
    function removeFromCart(id){
      delete cart[id]
      renderCart()
    }

    function renderCart(){
      const list = $('cartList')
      list.innerHTML = ''
      const ids = Object.keys(cart).map(x=>parseInt(x))
      if(ids.length === 0){
        $('cartEmpty').style.display = 'block'
        $('cartCount').innerText = '0'
        $('subtotal').innerText = formatMoney(0)
        return
      }
      $('cartEmpty').style.display = 'none'
      let subtotal = 0
      ids.forEach(id => {
        const p = products.find(x=>x.id===id)
        const qty = cart[id]
        subtotal += p.price * qty
        const item = document.createElement('div')
        item.className = 'cart-item'
        item.innerHTML = `
          <div style="width:56px;height:46px;border-radius:8px;overflow:hidden"><img src="${p.img}" style="width:100%;height:100%;object-fit:cover"/></div>
          <div style="flex:1">
            <div style="display:flex;justify-content:space-between;align-items:center"><div>${p.title}</div><div>${formatMoney(p.price)}</div></div>
            <div style="display:flex;gap:8px;align-items:center;margin-top:6px">
              <button class="qty-btn" onclick="changeQty(${id},-1)">-</button>
              <div style="min-width:22px;text-align:center">${qty}</div>
              <button class="qty-btn" onclick="changeQty(${id},1)">+</button>
              <button style="margin-left:auto;background:none;border:0;color:#e11d48;cursor:pointer" onclick="removeFromCart(${id})">Remove</button>
            </div>
          </div>
        `
        list.appendChild(item)
      })
      $('cartCount').innerText = ids.length
      $('subtotal').innerText = formatMoney(subtotal)
    }

    /***** Search & Filter *****/
    function getFilteredProducts(){
      const q = $('searchInput').value.trim().toLowerCase()
      const cat = $('categoryFilter').value
      return products.filter(p => {
        const matchQ = q === '' || p.title.toLowerCase().includes(q) || p.category.toLowerCase().includes(q)
        const matchCat = cat === 'all' || p.category === cat
        return matchQ && matchCat
      })
    }

    document.getElementById('searchInput').addEventListener('input', ()=>{
      renderProducts(getFilteredProducts())
    })
    document.getElementById('categoryFilter').addEventListener('change', ()=>{
      renderProducts(getFilteredProducts())
    })
    document.getElementById('clearBtn').addEventListener('click', ()=>{
      $('searchInput').value = ''
      $('categoryFilter').value = 'all'
      renderProducts(products)
    })

    document.getElementById('checkoutBtn').addEventListener('click', ()=>{
      const ids = Object.keys(cart)
      if(ids.length === 0) return alert('Your cart is empty')
      // In a real store you'd send this to a server or payment system. This is a demo.
      alert('Thanks for shopping with ' + SHOP_NAME + '!\n\nOrder summary:\n' + ids.map(id=>{
        const p = products.find(x=>x.id==id)
        return p.title + ' x' + cart[id]
      }).join('\n') + '\n\nSubtotal: ' + $('subtotal').innerText)
      // clear cart
      cart = {}
      renderCart()
    })

    // initial render
    renderProducts(products)
    renderCart()
  </script>
</body>
</html>
