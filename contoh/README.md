Berikut dua contoh konkret payload **discover → on_discover** (di Beckn biasa disebut **search → on_search**) untuk:

1. Domain **mobility** (ride-hailing)
2. Domain **local-retail** (belanja barang)

Semua contoh mengikuti struktur resmi spesifikasi Beckn (core 0.9.x / domain mobility & local-retail) yang digunakan di dokumentasi resmi Beckn.【turn10fetch0】【turn18fetch0】【turn14fetch0】【turn3fetch0】

---

## 1. Contoh payload: Mobility (ride-hailing)

### 1.1. Request dari BAP ke Gateway/BPP (`discover` / `search`)

Endpoint (Beckn v0.9.x):  
`POST /search` di Beckn Gateway atau langsung ke BPP.

**Use case**: pengguna mencari kendaraan dari lokasi A ke lokasi B.

```json
{
  "context": {
    "domain": "nic2004:60221",
    "country": "IDN",
    "city": "std:021",
    "action": "search",
    "core_version": "0.9.1",
    "bap_id": "https://ion-bap.example.com",
    "bap_uri": "https://ion-bap.example.com/beckn",
    "transaction_id": "txn-123456",
    "message_id": "msg-987654",
    "timestamp": "2026-05-28T08:00:00.000Z"
  },
  "message": {
    "intent": {
      "fulfillment": {
        "start": {
          "location": {
            "gps": "-6.229728,106.829549"
          }
        },
        "end": {
          "location": {
            "gps": "-6.175110,106.865039"
          }
        }
      }
    }
  }
}
```

Penjelasan singkat:

- `context.domain`: `nic2004:60221` adalah kode untuk domain **mobility** (taxi operation) di Beckn.【turn18fetch0】
- `action`: `search` → ini yang Anda sebut sebagai **“discover”**.
- `intent.fulfillment.start.location.gps`: titik penjemputan.
- `intent.fulfillment.end.location.gps`: titik tujuan.

Struktur ini persis seperti contoh resmi di dokumentasi Beckn untuk *“getting a list of mobility service providers that can transport a traveller from a pickup location to a drop location”*.【turn18fetch0】

---

### 1.2. Response dari BPP ke BAP (`on_discover` / `on_search`)

Endpoint: `POST /on_search` di BAP.

**Isi penting**: BPP mengirim **catalog** berisi provider (operator taksi) dan item (tipe kendaraan) yang cocok dengan intent di atas. Contoh berikut disederhanakan dari contoh resmi Beckn mobility.【turn3fetch0】【turn18fetch0】

```json
{
  "context": {
    "domain": "nic2004:60221",
    "country": "IDN",
    "city": "std:021",
    "action": "on_search",
    "core_version": "0.9.1",
    "bap_id": "https://ion-bap.example.com",
    "bap_uri": "https://ion-bap.example.com/beckn",
    "bpp_id": "https://ion-bpp-mobility.example.com",
    "bpp_uri": "https://ion-bpp-mobility.example.com/beckn",
    "transaction_id": "txn-123456",
    "message_id": "msg-987654-1",
    "timestamp": "2026-05-28T08:00:05.000Z"
  },
  "message": {
    "catalog": {
      "bpp/descriptor": {
        "name": "ION Mobility Provider"
      },
      "bpp/providers": [
        {
          "id": "blue-taxi",
          "descriptor": {
            "name": "Blue Taxi"
          },
          "locations": [
            {
              "id": "loc-blue-1",
              "gps": "-6.229728,106.829549"
            }
          ],
          "categories": [
            {
              "id": "ride-hailing",
              "descriptor": {
                "name": "Ride Hailing"
              }
            }
          ],
          "items": [
            {
              "id": "sedan-standard",
              "descriptor": {
                "name": "Standard Sedan",
                "images": [
                  "https://ion-bpp-mobility.example.com/images/sedan.jpg"
                ]
              },
              "category_id": "ride-hailing",
              "location_id": "loc-blue-1",
              "price": {
                "currency": "IDR",
                "value": "35000"
              },
              "time": {
                "label": "ETA",
                "duration": "PT10M"
              },
              "matched": true
            }
          ]
        }
      ]
    }
  }
}
```

Penjelasan:

- `action` berubah menjadi `on_search` (setara **“on_discover”**).
- `message.catalog` berisi:
  - `bpp/providers[]`: daftar operator (Blue Taxi).
  - `items[]`: daftar jenis kendaraan yang bisa dipilih user (Standard Sedan) lengkap dengan harga dan ETA.

Struktur ini mengikuti contoh `on_search` di spesifikasi mobility Beckn, termasuk penggunaan `bpp/providers`, `items`, `categories`, dan `locations`.【turn3fetch0】【turn18fetch0】

---

## 2. Contoh payload: Local-retail (belanja barang)

### 2.1. Request dari BAP (`discover` / `search`)

Domain yang digunakan di Beckn untuk local-retail adalah `nic2004:52110`.【turn10fetch0】【turn14fetch0】

```json
{
  "context": {
    "domain": "nic2004:52110",
    "country": "IDN",
    "city": "std:021",
    "action": "search",
    "core_version": "0.9.1",
    "bap_id": "https://ion-bap.example.com",
    "bap_uri": "https://ion-bap.example.com/beckn",
    "transaction_id": "txn-999888",
    "message_id": "msg-111222",
    "timestamp": "2026-05-28T08:05:00.000Z"
  },
  "message": {
    "intent": {
      "item": {
        "descriptor": {
          "name": "Green apple"
        }
      },
      "fulfillment": {
        "end": {
          "location": {
            "gps": "-6.229728,106.829549"
          }
        }
      }
    }
  }
}
```

Penjelasan:

- `intent.item.descriptor.name`: “Green apple” → barang yang dicari.
- `intent.fulfillment.end.location.gps`: lokasi pengiriman (alamat pembeli).

Struktur ini mengikuti contoh resmi **Search** di Beckn local-retail (pencarian “Green apple” dengan lokasi pengiriman).【turn10fetch0】

---

### 2.2. Response dari BPP (`on_discover` / `on_search`)

Berikut contoh **catalog** retail yang disederhanakan dari contoh resmi `on_search` local-retail Beckn.【turn14fetch0】

```json
{
  "context": {
    "domain": "nic2004:52110",
    "country": "IDN",
    "city": "std:021",
    "action": "on_search",
    "core_version": "0.9.1",
    "bap_id": "https://ion-bap.example.com",
    "bap_uri": "https://ion-bap.example.com/beckn",
    "bpp_id": "https://ion-bpp-retail.example.com",
    "bpp_uri": "https://ion-bpp-retail.example.com/beckn",
    "transaction_id": "txn-999888",
    "message_id": "msg-111222-1",
    "timestamp": "2026-05-28T08:05:05.000Z"
  },
  "message": {
    "catalog": {
      "bpp/descriptor": {
        "name": "ION Retail Provider"
      },
      "bpp/providers": [
        {
          "id": "toko-sejahtera",
          "descriptor": {
            "name": "Toko Sejahtera"
          },
          "locations": [
            {
              "id": "loc-toko-1",
              "gps": "-6.229500,106.829700"
            }
          ],
          "categories": [
            {
              "id": "fresh-fruits",
              "descriptor": {
                "name": "Fresh Fruits"
              }
            }
          ],
          "items": [
            {
              "id": "green-apple-1",
              "descriptor": {
                "name": "Green Apples Organic",
                "images": [
                  "https://ion-bpp-retail.example.com/images/green-apple.jpg"
                ]
              },
              "category_id": "fresh-fruits",
              "location_id": "loc-toko-1",
              "price": {
                "currency": "IDR",
                "value": "25000"
              },
              "matched": true
            }
          ]
        }
      ],
      "bpp/fulfillments": [
        {
          "type": "home-delivery"
        }
      ]
    }
  }
}
```

Penjelasan:

- Provider (`bpp/providers`): Toko Sejahtera.
- Item (`items`): “Green Apples Organic” dengan harga.
- `bpp/fulfillments.type`: “home-delivery” → menyatakan bahwa pengiriman ke alamat pembeli didukung.

Struktur ini mengikuti contoh resmi `on_search` di Beckn local-retail, termasuk `bpp/providers`, `categories`, `items`, dan `bpp/fulfillments`.【turn14fetch0】

---

## 3. Ringkasan alurnya

Dalam terminologi Beckn:

- **discover** = `search` (dari BAP ke Gateway/BPP).
- **on_discover** = `on_search` (dari BPP ke BAP).

Secara garis besar:

1. BAP mengirim `search` dengan `intent` (barang atau perjalanan + lokasi).
2. BPP memproses, lalu mengirim `on_search` berisi **catalog** (provider + item/layanan yang relevan).
3. BAP menampilkan hasil itu ke pengguna, lalu lanjut ke `select` → `on_select`, `init` → `on_init`, `confirm` → `on_confirm`, dan seterusnya.

Jika Anda mau, saya bisa lanjutkan dengan contoh payload untuk langkah berikutnya (`select`, `init`, `confirm`) di salah satu domain (mobility atau retail) agar alur transaksinya lengkap.
