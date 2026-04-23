# MongoDB Sample Data Archives

## TL;DR

```bash
export MONGO_MAJOR="7.0"
export DNS_SUFFIX="your-cluster.example.net"

wget -O "/tmp/sampledata-${MONGO_MAJOR}.archive" \
  "https://github.com/barronandersonmongo/sampledata/releases/download/v${MONGO_MAJOR}/sampledata-${MONGO_MAJOR}.archive"

mongorestore \
  --uri="mongodb://<username>:<password>@host1.${DNS_SUFFIX}:27017,host2.${DNS_SUFFIX}:27017,host3.${DNS_SUFFIX}:27017/?replicaSet=replSet&authSource=admin" \
  --archive="/tmp/sampledata-${MONGO_MAJOR}.archive" \
  --gzip

rm -f "/tmp/sampledata-${MONGO_MAJOR}.archive"
```

> Use the archive that matches your MongoDB server version.

---

This repository distributes **version-specific MongoDB sample data archives** via GitHub Releases.

These archives are intended for **testing, demos, and education**, and are designed to be restored using `mongorestore`.

---

## ⚠️ Version Compatibility

MongoDB does **not support cross-version dump and restore** between major versions.

> Always use the archive that matches your MongoDB server version.

Examples:

- MongoDB 7.0 → use release `v7.0`
- MongoDB 8.0 → use release `v8.0`

If you restore a newer archive into an older server, you may see warnings like:

```
WARNING: This archive came from MongoDB `X.X.X`, but you are restoring to `Y.Y.Y`. Cross-version dump & restore is unsupported. The restored data may be corrupted.
```

---

## 📦 Releases

All archives are published under the **Releases** section of this repository.

Each release contains:

- `sampledata-<version>.archive` (gzip-compressed MongoDB archive)

Example:
- `sampledata-7.0.archive`

---

## 🚀 Restore Instructions

### 1. Set variables

```bash
export MONGO_MAJOR="7.0"
export DNS_SUFFIX="your-cluster.example.net"
```

---

### 2. Download archive

```bash
wget -O "/tmp/sampledata-${MONGO_MAJOR}.archive" \
  "https://github.com/barronandersonmongo/sampledata/releases/download/v${MONGO_MAJOR}/sampledata-${MONGO_MAJOR}.archive"
```

---

### 3. Restore into your cluster

```bash
mongorestore \
  --uri="mongodb://<username>:<password>@host1.${DNS_SUFFIX}:27017,host2.${DNS_SUFFIX}:27017,host3.${DNS_SUFFIX}:27017/?replicaSet=replSet&authSource=admin" \
  --archive="/tmp/sampledata-${MONGO_MAJOR}.archive" \
  --gzip
```

---

### 4. Cleanup

```bash
rm -f "/tmp/sampledata-${MONGO_MAJOR}.archive"
```

---

## 📝 Notes

- Archives are created using `mongodump --archive --gzip`
- You **must** use `--gzip` when restoring
- The archive filename does not require a `.gz` extension
- System databases (`admin`, `config`, `local`) are excluded from published archives

---

## 🛠️ Creating a New Version (e.g. MongoDB 9.0)

This section documents the process used to generate new version-specific archives.

---

### Step 1: Create a Version-Specific Atlas Cluster

- Deploy an Atlas cluster running the target MongoDB version (e.g. 9.0)
- Load sample data using the Atlas UI (“Load Sample Dataset”)

---

### Step 2: Dump the Data

From the Atlas cluster:

```bash
mongodump \
  --uri="<your atlas connection string>" \
  --archive=sampledata-9.0.archive \
  --gzip
```

---

### Step 3: Spin Up a Local Ephemeral MongoDB Instance

Run a local instance of the **same MongoDB version**:

```bash
/path/to/mongod \
  --dbpath /tmp/sampledata-datadir
```

This instance is temporary and can be deleted after use.

---

### Step 4: Restore Locally with Filters

Restore while excluding system databases:

```bash
/path/to/mongorestore \
  --uri="mongodb://127.0.0.1:27017/" \
  --archive=sampledata-9.0.archive \
  --gzip \
  --nsExclude="admin.*" \
  --nsExclude="config.*" \
  --nsExclude="local.*"
```

---

### Step 5: Re-Dump Clean Archive

Create the final distributable archive:

```bash
mongodump \
  --uri="mongodb://127.0.0.1:27017/" \
  --archive=sampledata-9.0.archive \
  --gzip
```

---

### Step 6: Publish Release

- Create a new GitHub release:
  - Tag: `v9.0`
- Upload:
  - `sampledata-9.0.archive`

---

## 🔧 Troubleshooting

### Cross-version warnings

If you see a version mismatch warning during restore:

- You are using the wrong archive
- Download the correct version from Releases

---

## 📚 Tools Used

- `mongodump`
- `mongorestore`
- MongoDB Atlas

---

