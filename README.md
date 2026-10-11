# nestor.sale.com
avito:'https://www.avito.ru/all?q=гитара+Yamaha' },
];

/* ============ РЕНДЕР ============ */
const grid = document.getElementById('grid');
const empty = document.getElementById('empty');
const emptyQuery = document.getElementById('emptyQuery');
const avitoLink = document.getElementById('avitoLink');
let currentCat = 'all';
let currentQuery = '';

function render() {
  const q = currentQuery.trim().toLowerCase();

  const filtered = products.filter(p => {
    const matchCat = currentCat === 'all' || p.cat === currentCat;
    const matchQ = !q || p.title.toLowerCase().includes(q);
    return matchCat && matchQ;
  });

  grid.innerHTML = '';

  if (filtered.length === 0 && (q || currentCat !== 'all')) {
    // нет результатов — показываем блок с Avito
    emptyQuery.textContent = currentQuery || 'выбранной категории';
    avitoLink.href = 'https://www.avito.ru/all?q=' + encodeURIComponent(currentQuery || '');
    empty.classList.add('show');
    return;
  }

  empty.classList.remove('show');

  filtered.forEach(p => {
    const card = document.createElement('div');
    card.className = 'card';
    card.innerHTML = `
      <div class="card-img">${p.emoji}</div>
      <div class="card-body">
        <div class="card-title">${p.title}</div>
        <div class="card-price">${p.price.toLocaleString('ru-RU')} ₽</div>
        <div class="card-loc">📍 ${p.loc}</div>
        <button class="card-btn" onclick="openProduct(${p.id})">Подробнее</button>
      </div>
    `;
    grid.appendChild(card);
  });
}

/* ============ ПОИСК ============ */
function doSearch() {
  currentQuery = document.getElementById('searchInput').value;
  render();
  // если ничего не нашли — сразу ищем на авито
  const q = currentQuery.trim().toLowerCase();
  const found = products.some(p => p.title.toLowerCase().includes(q));
  if (q && !found) {
    // ничего не делаем — показываем блок с ссылкой
  } else if (q) {
    empty.classList.remove('show');
  }
}

document.getElementById('searchInput').addEventListener('keydown', e => {
  if (e.key === 'Enter') doSearch();
});

/* ============ КАТЕГОРИИ ============ */
document.querySelectorAll('.cat').forEach(el => {
  el.addEventListener('click', () => {
    document.querySelectorAll('.cat').forEach(c => c.classList.remove('active'));
    el.classList.add('active');
    currentCat = el.dataset.cat;
    render();
  });
});

/* ============ ТОВАР — КНОПКА ============ */
function openProduct(id) {
  const p = products.find(x => x.id === id);
  if (!p) return;
  // если у продавца есть ссылка на авито — открываем
  if (p.avito) {
    window.open(p.avito, '_blank');
  } else {
    alert('Товар: ' + p.title + '\nЦена: ' + p.price + ' ₽');
  }
}

/* ============ ДОНАТ ============ */
function openDonate(e) {
  if (e) e.preventDefault();
  document.getElementById('donateModal').classList.add('show');
}
function closeDonate() {
  document.getElementById('donateModal').classList.remove('show');
}
function copyPhone() {
  const phone = '+79269370843';
  navigator.clipboard.writeText(phone).then(showToast).catch(() => {
    // fallback
    const ta = document.createElement('textarea');
    ta.value = phone;
    document.body.appendChild(ta);
    ta.select();
    document.execCommand('copy');
    document.body.removeChild(ta);
    showToast();
  });
}
function showToast() {
  const t = document.getElementById('toast');
  t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), 1800);
}

// закрытие модалки по клику на фон
document.getElementById('donateModal').addEventListener('click', e => {
  if (e.target.id === 'donateModal') closeDonate();
});

/* ============ СТАРТ ============ */
render();
</script>
</body>
</html>
```