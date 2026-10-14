<!-- Source: https://www.tofupilot.com/docs/frameworks -->
# Frameworks on TofuPilot
URL: /docs/frameworks

Run OpenHTF, pytest, or Robot Framework on TofuPilot, or build your own connector with the Python, C#, C++, Rust, and MATLAB SDKs.

***

title: Frameworks on TofuPilot
sidebarTitle: Supported Frameworks
description: "Run OpenHTF, pytest, or Robot Framework on TofuPilot, or build your own connector with the Python, C#, C++, Rust, and MATLAB SDKs."
-------------------------------------------------------------------------------------------------------------------------------------------------

TofuPilot has first-class support for the most popular open-source test frameworks. You can run and stream test data using declarative or code-first frameworks, often without any upfront configuration.

## Deploy a template

[Get started](/docs/getting-started-with-tofupilot) by cloning a template to your favorite git provider and running it on a station.

<TemplateCards />

## Bring your own framework

Running an unsupported or in-house framework? Build your own connector to upload test data to TofuPilot with our open-source typed SDKs, available in [Python](/docs/sdk/python), [C#](/docs/sdk/csharp), [C++](/docs/sdk/cpp), [Rust](/docs/sdk/rust), and [MATLAB](/docs/sdk/matlab). For any other language, use the [REST API](/docs/api/rest-api) directly.

## Framework support matrix

The following table shows which features are supported by each framework on TofuPilot.

<FeatureMatrix
  frameworks={[
  { key: 'tofupilot', label: 'TofuPilot' },
  { key: 'openhtf', label: 'OpenHTF' },
  { key: 'pytest', label: 'Pytest' },
  { key: 'robot', label: 'Robot' },
]}
  categories={[
  {
    name: 'Data model',
    features: [
      {
        name: 'Phases',
        description: 'Ordered run steps, each with one outcome.',
        href: '/docs/fundamentals/test-data-model#phase',
        icon: <ListChecks className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'yes', pytest: 'yes', robot: 'yes' },
      },
      {
        name: 'Numeric measurements',
        description: 'Floats: voltages, temperatures, counts.',
        href: '/docs/fundamentals/test-data-model#measurement',
        icon: <IconNumber123 className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'yes', pytest: 'yes', robot: 'yes' },
      },
      {
        name: 'String measurements',
        description: 'Text: firmware versions, serials, status codes.',
        href: '/docs/fundamentals/test-data-model#measurement',
        icon: <IconLetterCase className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'yes', pytest: 'yes', robot: 'yes' },
      },
      {
        name: 'Boolean measurements',
        description: 'True/false flags and presence checks.',
        href: '/docs/fundamentals/test-data-model#measurement',
        icon: <IconToggleRight className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'yes', pytest: 'yes', robot: 'yes' },
      },
      {
        name: 'Logs',
        description: 'Level, timestamp, file, and line per message.',
        href: '/docs/fundamentals/test-data-model#log',
        icon: <Terminal className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'yes', pytest: 'yes', robot: 'yes' },
      },
      {
        name: 'Attachments',
        description: 'Scope captures, photos, diagnostic files.',
        href: '/docs/fundamentals/test-data-model#attachment',
        icon: <FileText className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'yes', pytest: 'no', robot: 'no' },
      },
      {
        name: 'Unit identification',
        description: 'Bind a serial number to the run via config or operator prompt.',
        href: '/docs/inventory/units',
        icon: <ScanQrCode className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'yes', pytest: 'yes', robot: 'yes' },
      },
      {
        name: 'Sub-units',
        description: 'Bind child units (board components) to the run.',
        href: '/docs/inventory/units',
        icon: <Puzzle className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'yes', pytest: 'yes', robot: 'yes' },
      },
      {
        name: 'Plugs',
        description: 'Instrument connections shared across phases.',
        href: '/docs/frameworks/tofupilot',
        icon: <Plug className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'yes', pytest: 'no', robot: 'no' },
      },
      {
        name: 'Multi-dimensional measurements',
        description: 'Waveforms and sweeps over X and Y axes.',
        href: '/docs/frameworks/tofupilot/measurements#multi-dimensional',
        icon: <ChartLine className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'yes', pytest: 'no', robot: 'no' },
      },
      {
        name: 'Aggregations',
        description: 'Mean, min, max, std over arrays.',
        href: '/docs/frameworks/tofupilot/measurements#aggregations',
        icon: <Sigma className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'no', pytest: 'no', robot: 'no' },
      },
      {
        name: 'Per-axis validators',
        description: 'Validate curve shape, not only endpoints.',
        href: '/docs/frameworks/tofupilot/measurements#validators',
        icon: <Ruler className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'no', pytest: 'no', robot: 'no' },
      },
    ],
  },
  {
    name: 'Operator UI',
    features: [
      {
        name: 'Simple custom forms',
        description: 'Text, number, switch, radio, select inputs.',
        href: '/docs/frameworks/tofupilot/operator-ui#inputs',
        icon: <TextCursorInput className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'no', pytest: 'no', robot: 'no' },
      },
      {
        name: 'Advanced forms & display components',
        description: 'Multiselect, checklist, image choice, sliders, progress.',
        href: '/docs/frameworks/tofupilot/operator-ui#displays',
        icon: <IconPhoto className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'no', pytest: 'no', robot: 'no' },
      },
    ],
  },
  {
    name: 'Execution',
    features: [
      {
        name: 'Retry & timeout',
        description: 'Bounded retries with delay; timeouts 1ms–24h.',
        href: '/docs/frameworks/tofupilot/phases#retry',
        icon: <RotateCcw className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'yes', pytest: 'no', robot: 'no' },
      },
      {
        name: 'Setup / teardown stages',
        description: 'Setup gates main; teardown always runs.',
        href: '/docs/frameworks/tofupilot/phases#setup-main-teardown',
        icon: <ListOrdered className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'yes', pytest: 'no', robot: 'no' },
      },
      {
        name: 'Parallel phases',
        description: 'Independent phases run in parallel; depends_on serializes.',
        href: '/docs/frameworks/tofupilot/phases#conditional-execution',
        icon: <Workflow className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'no', pytest: 'no', robot: 'no' },
      },
      {
        name: 'Conditional execution',
        description: 'Skip phases based on previous outcomes or runtime state.',
        href: '/docs/frameworks/tofupilot/phases#conditional-execution',
        icon: <GitBranch className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'yes', pytest: 'no', robot: 'no' },
      },
      {
        name: 'Multi-slot execution',
        description: 'Test multiple boards in parallel on one fixture.',
        href: '/docs/frameworks/tofupilot/execution#slots',
        icon: <Columns3 className="size-4" />,
        support: { tofupilot: 'yes', openhtf: 'no', pytest: 'no', robot: 'no' },
      },
    ],
  },
]}
/>
