# gaia-grpc-client

This is a GRPC client package of [cosmos gaia](https://github.com/cosmos/gaia) for Rust.

**Depended libraries**

- [tonic](https://github.com/hyperium/tonic)
- [tokio 1](https://github.com/tokio-rs/tokio)

**proto files**

- see [gaia/go.mod at v5.0.8 · cosmos/gaia](https://github.com/cosmos/gaia/blob/v5.0.8/go.mod) for listing related
packages versions.
- and also [cosmos-sdk/go.mod at v0.42.10](https://github.com/cosmos/cosmos-sdk/blob/v0.42.10/go.mod)

---

- [cosmos-sdk/proto at v0.42.10](https://github.com/cosmos/cosmos-sdk/tree/v0.42.10/proto)
- [tendermint/proto/tendermint at v0.34.13](https://github.com/tendermint/tendermint/tree/v0.34.13/proto/tendermint)
- [cosmos/cosmos-proto](https://github.com/cosmos/cosmos-proto)
- [ics23/proofs.proto at v0.6.6](https://github.com/confio/ics23/blob/v0.6.6/proofs.proto)
- [gogoproto + castrepeated option](https://github.com/gogo/protobuf/blob/caa11469d65478a29b37145d1bd9b4ae0d33d204/gogoproto/gogo.proto#L144)
- [google/api](https://fuchsia.googlesource.com/third_party/googleapis)

```bash
% find src/proto/cosmos -type f -follow -print | awk '{print "\""$0"\","}'
% find src/proto/ibc -type f -follow -print | awk '{print "\""$0"\","}'
```

## Installation

#### Dependencies

- [Rust with Cargo](http://rust-lang.org)
- [protoc](https://grpc.io/docs/protoc-installation/)

#### Importing

**Cargo.toml**

```toml
[dependencies]
gaia-grpc-client = { version = "5.0.8-beta.1", git = "https://github.com/kumanote/gaia-grpc-client-rs", branch = "main" }
```

**rust files**

```rust
use gaia_grpc_client::{cosmos, tonic};
```

## Usage

Here's a basic example:

```rust
use gaia_grpc_client::{cosmos, tonic};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let addr = "http://localhost:9090";
    let mut client =
        cosmos::base::tendermint::v1beta1::service_client::ServiceClient::connect(addr).await?;
    let request = tonic::Request::new(cosmos::base::tendermint::v1beta1::GetLatestBlockRequest {});
    let response = client.get_latest_block(request).await?;
    let latest_height = response.into_inner().block.unwrap().header.unwrap().height;
    let request = tonic::Request::new(cosmos::base::tendermint::v1beta1::GetBlockByHeightRequest {
        height: latest_height,
    });
    let response = client.get_block_by_height(request).await?;
    assert_eq!(
        latest_height,
        response.into_inner().block.unwrap().header.unwrap().height
    );
    Ok(())
}
```
