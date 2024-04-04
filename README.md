# Starting to understand Async and Await with an API call
In this project I followed a tutorial from Sean Allen on YouTube to undestand how to apply concurrency in SwiftUI making a call to a public API. 

## First thing I did: Create the UI
```ruby
VStack(spacing: 20){
            AsyncImage(url: URL(string: user?.avatarUrl ?? "")){ image in
                image
                    .resizable()
                    .aspectRatio(contentMode: .fit)
                    .clipShape(Circle())
            } placeholder: {
                Circle()
                    .foregroundColor(.secondary)
                    
            }
            .frame(width: 120, height: 120)
            Text(user?.login ?? "Username")
                .bold()
                .font(.title3)
            Text(user?.bio ?? "Bio")
            Spacer()
        }
        .padding()
```
It's a simple UI, because my main objective was to undesrtand how Async and Await works.

## Second thing I did: Create the structure of the User that the API will return
```ruby
struct GitHubUser: Codable {
    let login: String
    let avatarUrl: String
    let bio: String
}
```
In this step is important to put the exact variable that the JSON will return us, but it is important to always respect the camelcase. 

## Third thing I did: Make the function to call the API
```ruby
func getUser() async throws -> GitHubUser {
        let endpoint = "https://api.github.com/users/sallen0400"
        guard let url = URL(string: endpoint) else {
            throw GHError.invalidURL
        }
        let (data, response) = try await URLSession.shared.data(from: url)
        guard let response = response as? HTTPURLResponse, response.statusCode == 200 else {
            throw GHError.invalidResponse
        }
        
        do {
            let decoder = JSONDecoder()
            decoder.keyDecodingStrategy = .convertFromSnakeCase
            return try decoder.decode(GitHubUser.self, from: data)
        } catch {
            throw GHError.invalidData
        }
    }
```
It is important to specify the error that we might have on each part of the calling, so for that we enumarate some of the main errors we can get. 
```ruby
enum GHError: Error {
    case invalidURL
    case invalidResponse
    case invalidData
}
```
## Fourth thing I did: Connect API response with the UI
We have to create an optional variable to asure that our variable will always have a value. 
```ruby
 @State private var user: GitHubUser?
```
Now we can put the response in the UI
```ruby
 AsyncImage(url: URL(string: user?.avatarUrl ?? "")){ image in
                image
                    .resizable()
                    .aspectRatio(contentMode: .fit)
                    .clipShape(Circle())
            } placeholder: {
                Circle()
                    .foregroundColor(.secondary)
            }
```
```ruby
 Text(user?.login ?? "Username")
```
```ruby
 Text(user?.bio ?? "Bio")
```
## Further implementations
Create a search bar an connect the variable to the endpoint.
