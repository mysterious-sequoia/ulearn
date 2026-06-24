```mermaid
classDiagram
    namespace Builder {
        class GraphBuilderHandle {
            <<abstract>>
            -Graph graph
            #GraphBuilderHandle(Graph graph)
            +AddNode(string name) NodeBuilder
            +AddEdge(string from, string to) EdgeBuilder
            +Build() string
        }

        class DotGraphBuilder {
            +DirectedGraph(string name)$ DotGraphBuilder
            +UndirectedGraph(string name)$ DotGraphBuilder
        }

        class NodeBuilder {
            -GraphNode _node
            ~NodeBuilder(Graph graph, GraphNode node)
            +With(Action~NodeConfigurator~ configure) DotGraphBuilder
        }

        class EdgeBuilder {
            -GraphEdge edge
            ~EdgeBuilder(Graph graph, GraphEdge edge)
            +With(Action~EdgeConfigurator~ configure) DotGraphBuilder
        }

        class NodeConfigurator {
            -GraphNode node
            +NodeConfigurator(GraphNode node)
            +Color(string color) NodeConfigurator
            +Shape(NodeShape shape) NodeConfigurator
            +FontSize(int size) NodeConfigurator
            +Label(string label) NodeConfigurator
        }

        class EdgeConfigurator {
            -GraphEdge edge
            +EdgeConfigurator(GraphEdge edge)
            +Label(string label) EdgeConfigurator
            +FontSize(int size) EdgeConfigurator
            +Color(string color) EdgeConfigurator
            +Weight(double weight) EdgeConfigurator
        }

        class NodeShape {
            <<enumeration>>
            Box
            Ellipse
        }
    }

    namespace Provided {
        class Graph {
            -List~GraphEdge~ edges
            -Dictionary~string,GraphNode~ nodes
            +string GraphName
            +bool Directed
            +bool Strict
            +IEnumerable~GraphEdge~ Edges
            +IEnumerable~GraphNode~ Nodes
            +Graph(string graphName, bool directed, bool strict)
            +AddNode(string name) GraphNode
            +AddEdge(string sourceNode, string destinationNode) GraphEdge
        }

        class GraphNode {
            +Dictionary Attributes
            +string Name
            +GraphNode(string name)
        }

        class GraphEdge {
            +Dictionary Attributes
            +string SourceNode
            +string DestinationNode
            +bool Directed
            +GraphEdge(string sourceNode, string destinationNode, bool directed)
        }

        class DotFormatExtensions {
            <<static>>
            +ToDotFormat(Graph this)$ string
        }

        class DotFormatWriter {
            -TextWriter writer
            +DotFormatWriter(TextWriter writer)
            +Write(Graph graph)
            +Write(GraphNode node)
            +Write(GraphEdge edge)
            +WriteAttributes(IReadOnlyDictionary attributes)
            +EscapeId(string id)$ string
        }
    }

    GraphBuilderHandle <|-- DotGraphBuilder : наследует
    GraphBuilderHandle <|-- NodeBuilder : наследует
    GraphBuilderHandle <|-- EdgeBuilder : наследует
    GraphBuilderHandle <-- Graph : строит
    NodeBuilder <-- GraphNode : содержит
    EdgeBuilder <-- GraphEdge : содержит
    NodeConfigurator <-- GraphNode : конфигурирует
    EdgeConfigurator <-- GraphEdge : конфигурирует
    Graph <-- GraphNode : содержит
    Graph <-- GraphEdge : содержит
    NodeConfigurator ..> NodeShape : использует (принимает)
    GraphBuilderHandle ..> EdgeBuilder : возвращает (chaining)
    GraphBuilderHandle ..> NodeBuilder : возвращает (chaining)
    DotFormatExtensions ..> DotFormatWriter : использует для форматирования
    DotGraphBuilder ..> DotFormatExtensions : форматирует граф
    NodeBuilder ..> NodeConfigurator : передает в лямбду
    EdgeBuilder ..> EdgeConfigurator : передает в лямбду
```
