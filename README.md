# MinIO C++ Client SDK for Amazon S3 Compatible Cloud Storage [![Slack](https://slack.min.io/slack?type=svg)](https://slack.min.io) [![Sourcegraph](https://sourcegraph.com/github.com/minio/minio-cpp/-/badge.svg)](https://sourcegraph.com/github.com/minio/minio-cpp?badge) [![Apache V2 License](https://img.shields.io/badge/license-Apache%20V2-blue.svg)](https://github.com/minio/minio-cpp/blob/master/LICENSE)

MinIO C++ SDK is Simple Storage Service (aka S3) client to perform bucket and object operations to any Amazon S3 compatible object storage service.

For a complete list of APIs and examples, please take a look at the [MinIO C++ Client API Reference](https://minio-cpp.min.io/)

## Build requirements
* A working C++ development environment supporting C++17 standards.
* CMake 3.10 or higher.

## Install from `vcpkg`
```
vcpkg install minio-cpp
```

## Building source
```bash
$ git clone https://github.com/minio/minio-cpp
$ cd minio-cpp
$ cmake -B ./build -DCMAKE_BUILD_TYPE=Debug
$ cmake --build ./build --config Debug
```

## Example:: file-uploader.cc
```c++
#include <client.h>

int main(int argc, char* argv[]) {
  // Create S3 base URL.
  minio::http::BaseUrl base_url;
  base_url.SetHost("play.min.io");

  // Create credential provider.
  minio::creds::StaticProvider provider(
      "Q3AM3UQ867SPQQA43P2F", "zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG");

  // Create S3 client.
  minio::s3::Client client(base_url, &provider);

  std::string bucket_name = "asiatrip";

  // Check 'asiatrip' bucket exist or not.
  bool exist;
  {
    minio::s3::BucketExistsArgs args;
    args.bucket_ = bucket_name;

    minio::s3::BucketExistsResponse resp = client.BucketExists(args);
    if (!resp) {
      std::cout << "unable to do bucket existence check; " << resp.GetError()
                << std::endl;
      return EXIT_FAILURE;
    }

    exist = resp.exist_;
  }

  // Make 'asiatrip' bucket if not exist.
  if (!exist) {
    minio::s3::MakeBucketArgs args;
    args.bucket_ = bucket_name;

    minio::s3::MakeBucketResponse resp = client.MakeBucket(args);
    if (!resp) {
      std::cout << "unable to create bucket; " << resp.GetError() << std::endl;
      return EXIT_FAILURE;
    }
  }

  // Upload '/home/user/Photos/asiaphotos.zip' as object name
  // 'asiaphotos-2015.zip' to bucket 'asiatrip'.
  minio::s3::UploadObjectArgs args;
  args.bucket_ = bucket_name;
  args.object_ = "asiaphotos-2015.zip";
  args.filename_ = "/home/user/Photos/asiaphotos.zip";

  minio::s3::UploadObjectResponse resp = client.UploadObject(args);
  if (!resp) {
    std::cout << "unable to upload object; " << resp.GetError() << std::endl;
    return EXIT_FAILURE;
  }

  std::cout << "'/home/user/Photos/asiaphotos.zip' is successfully uploaded as "
            << "object 'asiaphotos-2015.zip' to bucket 'asiatrip'."
            << std::endl;

  return EXIT_SUCCESS;
}
```

## License
This SDK is distributed under the [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0), see [LICENSE](https://github.com/minio/minio-cpp/blob/master/LICENSE) for more information.
